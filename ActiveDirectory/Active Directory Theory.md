Active Directory Theory
========================================



<https://subscription.packtpub.com/book/security/9781801818933/4/ch04lvl1sec24/building-an-ad-red-team-lab>

# disabling antimalware protection and the domain firewall
Within our lab, we need to ensure that Windows Defender antimalware protection is disabled. Some techniques can be used to bypass antiviruses that will work today and tomorrow, and it will not be due to the continuous advancement of malware protection solutions. The following steps will guide you through the process of ensuring Windows Defender is disabled on all Windows systems:

On Windows Server 2019, open Server Manager | Tools | Group Policy Management and expand the forest until you see the redteamlab.local domain, as shown here:

# Apply policies windows 

    gpupdate