usr-bin-clipboard_edit.sh
========================

```
#!/bin/bash

sleep 0.4; xdotool type "$(xclip -o -selection clipboard | sed 's/<hostname>/ignite.thm/')"
notify-send "Clipboard" "Replacement done"

```

    sudo apt update
    sudo apt install xclip
 
    
    
![imagem](https://github.com/alisonocosta/InfraHacking/blob/main/QOwnNotesSync/.gitbook/assets/image%20(6).png)

![qownnotes-media-eIoWbp](../../../media/qownnotes-media-eIoWbp.png)


Depois é necessário configurar o shortcut via GUI mesmo:

Configurandoo o shortcut
