# SSH connections from a Windows host

This document outlines the procedure for using a private SSH key stored on an OnlyKey to establish a connection with a remote server.

> The provided examples are intended for Windows systems. However, the procedure can be easily adapted—albeit not always straightforward—for use on a Linux host.

## Preparation

* Download and unstall the last version of OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/)
* Install the [OnlyKey command line tools](https://docs.onlykey.io/command-line.html).

Verify the installed version of the OnlyKey command line tools by running: `onlykey-cli.exe version`.

Print the version of OpenSSH:

	C:\>where ssh-keygen
	C:\Windows\System32\OpenSSH\ssh-keygen.exe
	C:\Windows\System32\OpenSSH\ssh -V
	OpenSSH_for_Windows_9.5p1, LibreSSL 3.8.2

> General commands:
> * `onlykey-cli credential info`
> * `onlykey-cli credential ls`

## Create a FIDO authenticator-backed SSH key

Generate a FIDO authenticator-backed SSH key:

	ssh-keygen -t ed25519-sk -O resident -O application=ssh://my-servers -f %USERPROFILE%\.ssh\id_my_servers_sk

* `-t ed25519-sk`: type of key (type `ED25519` for security key - "sk" for "security key").
* `-O`: set option for generating **FIDO**-backed keys (see [this link](https://man.openbsd.org/ssh-keygen#FIDO_AUTHENTICATOR)).
  * `resident`: ensures that the private key is stored directly on the FIDO2 device as a resident key, making it persistently available for future authentication without reloading.
  * `application`: override the default FIDO application/origin string of "`ssh:`". This may be useful 
    when generating host or domain-specific resident keys. The specified application string must 
    begin with "`ssh:`".
* `-f %USERPROFILE%\.ssh\id_my_servers_sk`: Specifies the filename of the **key handle** (see below).

> **Notes**
>
> **FIDO** (**F**ast **ID**entity **O**nline) is a set of open, standardized authentication protocols intended to ultimately eliminate the use of passwords for authentication.
>
> `ssh-keygen` is able to generate **FIDO** authenticator-backed keys, after which they may be used 
> much like any other key type supported by OpenSSH, so long as the hardware authenticator is 
> attached when the keys are used. FIDO authenticators generally require the user to explicitly
> authorise operations by touching or tapping them.
>
> **FIDO** keys consist of **two parts**:
> * A **key handle** (credential ID), stored on the host system, which acts as a reference to a key pair securely managed by the FIDO authenticator (e.g., the OnlyKey).
> * A **device-bound private key**, generated and stored securely within the FIDO authenticator, which cannot be extracted or exported from the hardware.
>
> These are combined by the hardware at authentication time to derive the real key that is used to 
> sign authentication challenges. Supported key types are `ecdsa-sk` and `ed25519-sk`.
>
> There are three different types of FIDO authentication protocols available.
> - Universal Authentication Framework (**UAF**), allows users to authenticate using a passwordless method, such as facial or voice recognition, fingerprint reading, or entering a PIN.
> - Universal Second Factor (**U2F**), users provide two pieces of information to authenticate: something that they know, like a username and password, and something they have, like a USB device.
> - **FIDO2** combines the capabilities of **UAF** and **U2F** into new protocols that provide users with passwordless authentication experiences, or two-factor and multi-factor authentication (2FA and MFA) experiences if additional protection is needed. These experiences might involve embedded authenticators, such as biometrics or PINs, or roaming authenticators, such as fobs or USB devices.
>
> See [this link](https://www.pingidentity.com/en/resources/identity-fundamentals/authentication/passwordless-authentication/fido.html).


Next, verify that the FIDO-backed SSH key has been successfully created.

The **key handle** (credential ID):

	C:\>dir %USERPROFILE%\.ssh\id_my_servers_sk
	...
	08/05/2025  22:34               553 id_my_servers_sk

The **device-bound private key**:

	C:\keepass>onlykey-cli credential ls
	PIN:
	Relying Party       Username            Credential ID
	-----------------------------------------------------
	...
	ssh://my-servers    openssh             CFC...

## Extract the public key from the FIDO authenticator-backed SSH key

Just execute the following command:

	ssh-keygen -y -f %USERPROFILE%\.ssh\id_my_servers_sk > %USERPROFILE%\.ssh\id_my_servers_sk.pub

The public key associated with the FIDO authenticator-backed SSH key is stored in the file "`%USERPROFILE%\.ssh\id_my_servers_sk.pub`".

## Install the public key on a remote host

	type %USERPROFILE%\.ssh\id_my_servers_sk.pub | ssh user@remote-host "cat >> .ssh/authorized_keys"

## Connect to the remote host

	ssh -o StrictHostKeychecking=no -o IdentitiesOnly=yes -o IdentityFile="%USERPROFILE%\.ssh\id_my_servers_sk" user@remote-host

