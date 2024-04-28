# Hashes types and cracking them

sources: https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4
http://www.adshotgyan.com/2012/02/lm-hash-and-nt-hash.html
https://security.stackexchange.com/questions/76383/identifying-hash-format-caputred-via-metasploit
https://security.stackexchange.com/questions/178888/ntlmv2-hash-harvesting-remotely
https://0xdf.gitlab.io/2019/01/13/getting-net-ntlm-hases-from-windows.html
https://byt3bl33d3r.github.io/practical-guide-to-ntlm-relaying-in-2017-aka-getting-a-foothold-in-under-5-minutes.html
https://mgp25.com/research/infosec/Leaking-NTLM-hashes/
Bilble
http://davenport.sourceforge.net/ntlm.html

### zip

    zip2john bank_account.zip > zip.hashes
    john --wordlist=/usr/share/wordlists/rockyou.txt zip.hashes


# hashid


    hashid c43ee559d69bc7f691fe2fbfe8a5ef0a

![qownnotes-media-iGYHcO](../../media/qownnotes-media-iGYHcO.png)

    hashid '$6$l5bL6XIASslBwwUD$bCxeTlbhTH76wE.bI66aMYSeDXKQ8s7JNFwa1s1KkTand6ZsqQKAF3G0tHD9bd59e5NAz/s7DQcAojRTWNpZX0'

![qownnotes-media-gbTqVR](../../media/qownnotes-media-gbTqVR.png)

Importantíssimo aprendizado:

## UNSHADOW (Linux cracking hashes)

    unshadow /etc/passwd /etc/shadow > hashes.john
    john --format=sha256crypt -w /usr/share/wordlists/rockyou.txt hashes.txt

    john --format=sha256crypt -w /usr/share/wordlists/rockyou.txt shadow

![qownnotes-media-rlUmkO](../../media/qownnotes-media-rlUmkO.png)


### LM

Example
	299BD128C1101FD6

The algorithm

1. Convert all lower case to upper case
2. Pad password to 14 characters with NULL characters
3. Split the password to two 7 character chunks
4. Create two DES keys from each 7 character chunk
5. DES encrypt the string "KGS!@#$%" with these two chunks
6. Concatenate the two DES encrypted strings. This is the LM hash.

Cracking it
	john --format=lm hash.txt
	hashcat -m 3000 -a 3 hash.txt

### NTHash (NTLM)
Example
	B4B9B02E6F09A9BD760F388B67351E2B

The algorithm

MD4(UTF-16-LE(password))

 Cracking it
 
     john --format=nt hash.txt
	    hashcat -m 1000 -a 3 hash.txt
	
#### NTLMv1 (A.K.A. Net-NTLMv1)
Example
	u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:9526fb8c23a90751cdd619b6cea564742e1e4bf33006ba41:cb8086049ec4736c
	
Where
<user_name>:<user_id>:<LM>:<NT>:<Commments>:<Home dir>
The algorithm

C = 8-byte server challenge, random
K1 | K2 | K3 = LM/NT-hash | 5-bytes-0
response = DES(K1,C) | DES(K2,C) | DES(K3,C)

Cracking it
	john --format=netntlm hash.txt
	hashcat -m 5500 -a 3 hash.txt
	
#### NTLMv2 (AKA. Net-NTLMv2)
Example
	admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c7830315c7830310000000000000b45c67103d07d7b95acd12ffa11230e0000000052920b85f78d013c31cdb3b92f5d765c783030

The algorithm

SC = 8-byte server challenge, random
CC = 8-byte client challenge, random
CC* = (X, time, CC2, domain name)
v2-Hash = HMAC-MD5(NT-Hash, user name, domain name)
LMv2 = HMAC-MD5(v2-Hash, SC, CC)
NTv2 = HMAC-MD5(v2-Hash, SC, CC*)
response = LMv2 | CC | NTv2 | CC*

Cracking it

	john --format=netntlmv2 hash.txt
	hashcat -m 5600 -a 3 hash.txt