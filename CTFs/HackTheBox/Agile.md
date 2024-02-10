Agile
========================

echo "10.10.11.203    superpass.htb" >> /etc/hosts

    sudo nmap_enum superpass.htb

![qownnotes-media-ymWeNs](../../../media/qownnotes-media-ymWeNs.png)

    nikto -h http://superpass.htb -Tuning x 6

![qownnotes-media-bFzTee](../../../media/qownnotes-media-bFzTee.png)

    dirb http://superpass.htb

    firefox superpass.htb
 
 ![qownnotes-media-bivGbv](../../../media/qownnotes-media-bivGbv.png)

Depois fiz um registro de usuario por meio da interface web e entrei na aplicacao e rodei um dirb autenticado

    ┌──(acosta㉿laboratorio)-[/home/kali/ownCloud/owncloud/Area_de_trabalho/personal_work/hackTheBox/agile]
    └─$ dirb http://superpass.htb -c "remember_token=14|c84d5cbe8e8990d07681453bb8030fd4ae0a994e3a974028fd4ad6ac1d871f0bd41a7c5618410e5d1f108a17e1001a38887571eb341820915af4ca295194a448;session:.eJwlzksOwjAMBcC7ZM0iHzt56WWQ3fgJti1dIe4OEnOCeac7jzgfaXsdV9zS_bnSliA6m6BYZHabAMpilh3Rmlnt0RYCLqFFJ3vlkKAajdBKYuV9lmZVws21L0rRHAtCUBXBOtCzOjjKaC7u-xoKNfjkEqZf5Drj-G-KpM8XGPgwbA.ZAdetg.EL1ZwWmKRQ7cGB-xBkHxZZ_1JR4"

![qownnotes-media-tPLMVe](../../../media/qownnotes-media-tPLMVe.png)

![qownnotes-media-CrrRHN](../../../media/qownnotes-media-CrrRHN.png)

Aqui descobrimos um parametro na aplicacao "fn" onde podemos identificar um LFI

![qownnotes-media-vGDBqp](../../../media/qownnotes-media-vGDBqp.png)
