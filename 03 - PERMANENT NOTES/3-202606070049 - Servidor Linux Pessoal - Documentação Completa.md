---
id: 3-202606070049
tags:
  - nota-permanente-nao-literaria
relacao: "[[3-202606070043 - Sistema TTS no Servidor - Guia Prático]]"
tema: "[[3 - I.T]]"
---
# Hardware

|Componente|Detalhe|
|---|---|
|Sistema operacional|Ubuntu Server|
|Disco do sistema|`/dev/sda2` — 25 GB|
|Disco de dados|`/dev/nvme0n1p1` — 98 GB, montado em `/dados`|
|RAM|~8 GB|
|Swap|7.7 GB (em `/dados/swapfile`)|

---
# Serviços rodando no servidor

| Serviço             | Função                      | Porta  |
| ------------------- | --------------------------- | ------ |
| XTTS-v2             | Motor de conversão TTS      | 8880   |
| Watcher             | Monitora a pasta e converte | —      |
| Nextcloud           | Sincroniza arquivos         | 8080   |
| Pi-hole             | Bloqueio de anúncios DNS    | 8053   |
| Nginx Proxy Manager | Gerenciador de proxy        | 80/443 |
| Portainer           | Gerenciador Docker visual   | 9000   |

---

# Acesso remoto

- **SSH** — acesso via terminal pelo Windows
- **Tailscale** — VPN mesh com IP fixo privado (`100.x.x.x`), acessível de qualquer dispositivo com Tailscale instalado sem abrir portas no roteador

---

# Estrutura de diretórios importante

```
/dados/
├── docker/              → Docker root (imagens, volumes, containers)
├── containerd/          → Containerd root
├── swapfile             → Arquivo de swap
└── tts-stack/           → Projeto TTS
    ├── docker-compose.yml
    ├── Dockerfile.watcher
    └── scripts/
        └── watcher.py

/home/henri/docker/
├── nextcloud/           → docker-compose do Nextcloud + MariaDB
├── nginx/               → docker-compose do Nginx Proxy Manager
└── pihole/              → docker-compose do Pi-hole
```

---

# Serviços rodando (Docker)

Todos têm `restart: always` e sobem automaticamente com o servidor.

|Container|Imagem|Função|Porta|
|---|---|---|---|
|`xtts-api`|`ghcr.io/coqui-ai/xtts-streaming-server:latest-cpu`|Motor TTS multilíngue|8880|
|`tts-watcher`|`tts-stack-watcher` (local)|Monitora pasta e converte TXT→MP3|—|
|`nextcloud-nextcloud-1`|`nextcloud:latest`|Nuvem pessoal e sync de arquivos|8080|
|`nextcloud-nextcloud-db-1`|`mariadb:10.11`|Banco de dados do Nextcloud|—|
|`pihole-pihole-1`|`pihole/pihole:latest`|Bloqueio de anúncios via DNS|8053|
|`nginx-nginx-proxy-manager-1`|`jc21/nginx-proxy-manager:latest`|Proxy reverso com SSL|80/443|
|`portainer`|`portainer/portainer-ce:latest`|Interface visual Docker|9000|

---

# Configuração do Docker

Movidos para o NVMe para não lotar o disco do sistema:

```
Docker root:       /dados/docker
Containerd root:   /dados/containerd
Config Docker:     /etc/docker/daemon.json
Config Containerd: /etc/containerd/config.toml
```

Habilitados no boot:

bash

```bash
sudo systemctl enable docker
sudo systemctl enable tailscaled
```

---

# Nextcloud

- **URL:** `http://IP-TAILSCALE:8080`
- **Usuário:** `admin`
- **Volume de dados:** `/dados/docker/volumes/nextcloud_nextcloud_data/_data`
- **Compose:** `~/docker/nextcloud/docker-compose.yml`
- **Pastas TTS:**
    - `tts-input` → você coloca os `.txt` aqui
    - `tts-output` → os MP3s aparecem aqui automaticamente

---

# Sistema TTS

[[3-202606070043 - Sistema TTS no Servidor - Guia Prático]]

---

# Pi-hole

- **URL:** `http://IP-TAILSCALE:8053`
- Servidor DNS local com bloqueio de anúncios e rastreadores
- **Compose:** `~/docker/pihole/docker-compose.yml`

---

# Nginx Proxy Manager

- **URL admin:** `http://IP-TAILSCALE:81`
- Gerencia proxy reverso e certificados SSL
- **Compose:** `~/docker/nginx/docker-compose.yml`

---

# Portainer

- **URL:** `http://IP-TAILSCALE:9000`
- Interface web para gerenciar todos os containers visualmente
- Iniciado via `docker run` (sem compose próprio)

---
# Serviços rodando no servidor

| Serviço             | Função                      | Porta  |
| ------------------- | --------------------------- | ------ |
| XTTS-v2             | Motor de conversão TTS      | 8880   |
| Watcher             | Monitora a pasta e converte | —      |
| Nextcloud           | Sincroniza arquivos         | 8080   |
| Pi-hole             | Bloqueio de anúncios DNS    | 8053   |
| Nginx Proxy Manager | Gerenciador de proxy        | 80/443 |
| Portainer           | Gerenciador Docker visual   | 9000   |

---

# Comandos de manutenção

bash

```bash
# Ver todos os containers rodando
docker ps

# Ver uso de espaço em disco
df -h / && df -h /dados
docker system df

# Logs do TTS em tempo real
cd /dados/tts-stack && docker compose logs -f watcher

# Reiniciar serviços individualmente
cd /dados/tts-stack && docker compose restart watcher
cd ~/docker/nextcloud && docker compose restart
cd ~/docker/pihole && docker compose restart
cd ~/docker/nginx && docker compose restart

# Forçar indexação do Nextcloud (raramente necessário)
docker exec -u www-data nextcloud-nextcloud-1 php occ files:scan admin

# Limpeza de espaço
sudo apt clean && sudo apt autoremove -y
docker system prune -f
```