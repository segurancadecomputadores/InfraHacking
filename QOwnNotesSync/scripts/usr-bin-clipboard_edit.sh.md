# /usr/bin/clipboard\_edit.sh

```
#!/bin/bash

sleep 0.4; xdotool type "$(xclip -o -selection clipboard | sed 's/<hostname>/ignite.thm/')"
notify-send "Clipboard" "Replacement done"
```

Aqui são necessários algumas dependências para resolver esse problema:

```
sudo apt update
sudo apt install xclip
```

Depois é necessário configurar o shortcut via GUI mesmo:

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>Configurandoo o shortcut</p></figcaption></figure>

