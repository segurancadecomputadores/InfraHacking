AD Lab
========================

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