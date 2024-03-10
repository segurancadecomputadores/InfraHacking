AD Lab
========================


Baixar a ISO do site da microsoft:

<https://go.microsoft.com/fwlink/p/?LinkID=2195167&clcid=0x409&culture=en-us&country=US>

Dados de cadastro

John
Doe

Saudi Arabia

Saudi Arabia 

[2024-03-08 16-57-03.mkv](../.gitbook/assets/2024-03-08 16-57-03-2.mkv)

{%embed url="https://github.com/alisonocosta/InfraHacking/blob/main/.gitbook/assets/2024-03-08%2016-57-03-1.mkv" %}

Adicionar os usuários

```
# Defina as informações necessárias
$dominio = "seuDominio"
$unidadeOrganizacional = "OU=Usuarios,DC=dominio,DC=com"
$senhaPadrao = ConvertTo-SecureString -String "Senha@123" -AsPlainText -Force

# Loop para criar 10 usuários
for ($i = 1; $i -le 10; $i++) {
    $nomeUsuario = "Usuario$i"
    $nomeCompleto = "Nome$i Sobrenome$i"

    # Crie o usuário
    New-ADUser -SamAccountName $nomeUsuario -UserPrincipalName "$nomeUsuario@$dominio" -Name $nomeCompleto `
        -GivenName "Nome$i" -Surname "Sobrenome$i" -Enabled $true -AccountPassword $senhaPadrao `
        -ChangePasswordAtLogon $true -Department "TI" -Path $unidadeOrganizacional

    Write-Host "Usuário $nomeUsuario criado com sucesso."
}

```
