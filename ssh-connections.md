# SSH connections from a Windows host

## Preparation

* Download and unstall the last version of OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/)
* Install the [OnlyKey command line tools](https://docs.onlykey.io/command-line.html).

Check the version of the command line tools: `onlykey-cli.exe version`.

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
  * `resident`: indicate that the key handle should be stored on the FIDO authenticator itself. This makes it easier to use the authenticator on multiple computers. Resident keys may be supported on FIDO2 authenticators and typically require that a PIN be set on the authenticator prior to generation. Resident keys may be loaded off the authenticator using ssh-add(1). Storing both parts of a key on a FIDO authenticator increases the likelihood of an attacker being able to use a stolen authenticator device.
  * `application`: override the default FIDO application/origin string of "`ssh:`". This may be useful when generating host or domain-specific resident keys. The specified application string must begin with "`ssh:`".
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
> * a **key handle** part stored in the private key file on disk.
> * a **per-device private key** that is unique to each FIDO authenticator and that cannot be exported
>   from the authenticator hardware.
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

Then check that the **FIDO** authenticator-backed SSH key has been created.
Remember that the **FIDO** consist of **two parts**:
* The **key handle** part stored in the private key file on disk.
* The **per-device private key** that is unique to each FIDO authenticator and
  that cannot be exported from the authenticator hardware.

The **key handle**:

	C:\>dir %USERPROFILE%\.ssh\id_my_servers_sk
	...
	08/05/2025  22:34               553 id_my_servers_sk

The **per-device private key**:

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

## Install the public key on a remote server

