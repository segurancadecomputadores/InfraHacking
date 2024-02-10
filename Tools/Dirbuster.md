Dirbuster
========================

# Basic usage

    dirb https://vivo.ccom.br

# Custom wordlist

    dirb https://vivomaisfamilia.com.br PHP.fuzz.txt

# User agent (bypassing WAF)

    dirb https://vivomaisfamilia.com.br PHP.fuzz.txt -a "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36"

# Utilizando cookie
Primeiro temos que pegar os cookies por meio de uma ferramenta do firefox:

https://addons.mozilla.org/en-US/firefox/addon/cookie-quick-manager/

![qownnotes-media-hZvfPS](../../media/qownnotes-media-hZvfPS.png)


    dirb http://superpass.htb -c "remember_token=14|c84d5cbe8e8990d07681453bb8030fd4ae0a994e3a974028fd4ad6ac1d871f0bd41a7c5618410e5d1f108a17e1001a38887571eb341820915af4ca295194a448;session:.eJwlzksOwjAMBcC7ZM0iHzt56WWQ3fgJti1dIe4OEnOCeac7jzgfaXsdV9zS_bnSliA6m6BYZHabAMpilh3Rmlnt0RYCLqFFJ3vlkKAajdBKYuV9lmZVws21L0rRHAtCUBXBOtCzOjjKaC7u-xoKNfjkEqZf5Drj-G-KpM8XGPgwbA.ZAdetg.EL1ZwWmKRQ7cGB-xBkHxZZ_1JR4"