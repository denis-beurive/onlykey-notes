# SSH connections from a Windows host

**Download**: https://github.com/PowerShell/Win32-OpenSSH/

Install [OnlyKey command line tools](https://docs.onlykey.io/command-line.html).

Check the version of the command line tools: `onlykey-cli.exe version`.

Print the version of OpenSSH:

	C:\>where ssh-keygen
	C:\Windows\System32\OpenSSH\ssh-keygen.exe
	C:\Windows\System32\OpenSSH\ssh -V
	OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2

General commands:

* `onlykey-cli credential info`
* `onlykey-cli credential ls`

Generate an SSH key:

	ssh-keygen -t ed25519-sk -O resident -O application=ssh://my-servers -f %USERPROFILE%\.ssh\id_my_servers_sk

* `-t ed25519-sk`: type of key (type `ED25519` for security key - "sk" for "security key").
* `-O resident`: 
* `-f %USERPROFILE%\.ssh\id_my_servers_sk`: 

Then check that the key has been created and transferred to the OnlyKey:

	C:\keepass>onlykey-cli credential ls

	PIN:
	Relying Party       Username            Credential ID
	-----------------------------------------------------
	...
	ssh://my-servers    openssh             CFC...

	C:\>dir %USERPROFILE%\.ssh\id_my_servers_sk
	...
	08/05/2025  22:34               553 id_my_servers_sk

	C:\>type %USERPROFILE%\.ssh\id_my_servers_sk
	-----BEGIN OPENSSH PRIVATE KEY-----
	...
	-----END OPENSSH PRIVATE KEY-----
