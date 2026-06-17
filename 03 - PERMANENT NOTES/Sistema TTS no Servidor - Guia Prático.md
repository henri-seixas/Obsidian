---
id: 3-202606070043
tags:
  - nota-permanente-nao-literaria
relacao: "[[Servidor Linux Pessoal - Documentação Completa]]"
tema: "[[3 - I.T]]"
---
# O que é e como funciona

Você tem um servidor Linux que converte arquivos de texto em áudio MP3 automaticamente. Tudo funciona sozinho, sem precisar abrir o servidor ou rodar comandos.

---

# Fluxo completo

```
Você cria um .txt com nome correto
        ↓
Coloca na pasta tts-input do Nextcloud
        ↓
Nextcloud sincroniza com o servidor
        ↓
Servidor converte para MP3 automaticamente
        ↓
MP3 aparece na pasta tts-output do Nextcloud
        ↓
Sincroniza no Windows e celular
```

---

# Como nomear os arquivos

O nome do arquivo define o idioma e a voz:

```
IDIOMA_VOZ_nome_que_quiser.txt
```

**Exemplos:**

```
en_craig_chapter1.txt       → Inglês, voz Craig Gutsy
fr_ana_mon_texte.txt        → Francês, voz Ana Florence
de_aaron_mein_text.txt      → Alemão, voz Aaron Dreschner
es_sofia_mi_texto.txt       → Espanhol, voz Sofia Hellen
ru_viktor_tekst.txt         → Russo, voz Viktor Eka
it_gitta_il_testo.txt       → Italiano, voz Gitta Nikolina
pt_gilberto_meu_texto.txt   → Português, voz Gilberto Mathias
arquivo_sem_prefixo.txt     → Inglês, voz Claribel Dervla (padrão)
```

---
# Compatibilidade vozes × idiomas

**Todas as 58 vozes funcionam em todos os 7 idiomas.** O XTTS-v2 usa clonagem de voz entre idiomas — o sotaque e timbre da voz se mantêm independente do idioma escolhido.

A qualidade pode variar levemente dependendo da combinação. Sugestões testadas e recomendadas por idioma:

|Idioma|Vozes recomendadas|
|---|---|
|Inglês `en`|`craig`, `aaron`, `ana`, `nova`, `damien`|
|Francês `fr`|`ana`, `camilla`, `baldur`, `zacharie`|
|Alemão `de`|`aaron`, `wulf`, `gitta`, `tanja`|
|Italiano `it`|`gilberto`, `gitta`, `sofia`, `ferran`|
|Espanhol `es`|`sofia`, `luis`, `marcos`, `alma`|
|Russo `ru`|`viktor`, `ludvig`, `lidiya`, `asya`|
|Português `pt`|`gilberto`, `marcos`, `claribel`, `brenda`|

Essas são sugestões baseadas na origem fonética dos nomes das vozes. Qualquer voz pode ser usada em qualquer idioma — experimente e escolha a que preferir.

---

# Códigos de idioma

|Código|Idioma|
|---|---|
|`en`|Inglês|
|`fr`|Francês|
|`de`|Alemão|
|`it`|Italiano|
|`es`|Espanhol|
|`ru`|Russo|
|`pt`|Português|

---

# Vozes disponíveis (use o primeiro nome)

## Lista completa de vozes (58 disponíveis)

**Femininas**

|Prefixo|Voz completa|
|---|---|
|`claribel`|Claribel Dervla ⭐ padrão|
|`ana`|Ana Florence|
|`sofia`|Sofia Hellen|
|`daisy`|Daisy Studious|
|`gracie`|Gracie Wise|
|`tammie`|Tammie Ema|
|`alison`|Alison Dietlinde|
|`annmarie`|Annmarie Nele|
|`asya`|Asya Anara|
|`brenda`|Brenda Stern|
|`gitta`|Gitta Nikolina|
|`henriette`|Henriette Usha|
|`tammy`|Tammy Grit|
|`tanja`|Tanja Adelina|
|`vjollca`|Vjollca Johnnie|
|`nova`|Nova Hogarth|
|`maja`|Maja Ruoho|
|`uta`|Uta Obando|
|`lidiya`|Lidiya Szekeres|
|`chandra`|Chandra MacFarland|
|`szofi`|Szofi Granger|
|`camilla`|Camilla Holmström|
|`lilya`|Lilya Stainthorpe|
|`zofija`|Zofija Kendrick|
|`narelle`|Narelle Moon|
|`barbora`|Barbora MacLean|
|`alexandra`|Alexandra Hisakawa|
|`alma`|Alma María|
|`rosemary`|Rosemary Okafor|

**Masculinas**

|Prefixo|Voz completa|
|---|---|
|`craig`|Craig Gutsy|
|`aaron`|Aaron Dreschner|
|`viktor`|Viktor Eka|
|`abrahan`|Abrahan Mack|
|`damien`|Damien Black|
|`gilberto`|Gilberto Mathias|
|`luis`|Luis Moray|
|`marcos`|Marcos Rudaski|
|`andrew`|Andrew Chipper|
|`badr`|Badr Odhiambo|
|`dionisio`|Dionisio Schuyler|
|`royston`|Royston Min|
|`adde`|Adde Michal|
|`baldur`|Baldur Sanjin|
|`ilkin`|Ilkin Urbano|
|`kazuhiko`|Kazuhiko Atallah|
|`ludvig`|Ludvig Milivoj|
|`suad`|Suad Qasim|
|`torcull`|Torcull Diarmuid|
|`zacharie`|Zacharie Aimilios|
|`ige`|Ige Behringer|
|`filip`|Filip Traverse|
|`damjan`|Damjan Chapman|
|`wulf`|Wulf Carlevaro|
|`kumar`|Kumar Dahl|
|`eugenio`|Eugenio Mataraci|
|`ferran`|Ferran Simen|
|`xavier`|Xavier Hayasaka|

---

# Pode enviar vários arquivos de uma vez?

Sim. Coloque quantos quiser na pasta `tts-input`. O sistema processa um por vez na ordem que chegam.

---

# Precisa fazer algo manualmente?

Não. O sistema roda sozinho 24/7 e sobe automaticamente quando o servidor reinicia.

---

# Comandos úteis (somente se precisar verificar algo)

bash

```bash
# Ver se todos os serviços estão rodando
docker ps

# Ver conversões em tempo real
cd /dados/tts-stack && docker compose logs -f watcher

# Ver MP3s gerados
sudo ls -lh "/dados/docker/volumes/nextcloud_nextcloud_data/_data/data/admin/files/tts-output/"

# Forçar o Nextcloud a indexar manualmente (raramente necessário)
docker exec -u www-data nextcloud-nextcloud-1 php occ files:scan admin
```

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
