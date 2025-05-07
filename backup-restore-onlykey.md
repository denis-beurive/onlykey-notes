# Backup / Restore

## Prerequisite

* you must set a backup passphrase (to the key being backed up) prior to back up a key.
* you must set a backup passphrase (to the key upon which the backup is restored) prior to restore a back up.
* if you restore key `K1`’s backup to key `K2`, the same backup passphrase must be set on `K1` and `K2`.

## Backup

Online documentation: [Secure Encrypted Backup Anywhere](https://docs.onlykey.io/usersguide.html#restore-from-backup)

Procedure:
* Insert the key into the USB slot.
* Enter the PIN (using the 6 keys on the device). The LED is green.
* Open the desktop application.
* Select the tab `Backup/Restore`.
* Click on the text area `Backup data`.
* Hold the #1 button down on your OnlyKey for 5+ seconds and release. 
* Wait until the operation ends.

> Please note: you don't have to use the desktop application to back up the OnlyKey. You can, for example, open a text editor (notepad, for example), click in the text area (in the text editor), and hold the #1 button down on your OnlyKey for 5+ seconds and release. The text that represents the backup will be written directly in the text editor.

> Please note: for windows users, you can use PoweShell to copy the content of the backup file to the clipboard.
> For example:
>
> ```
> PS C:> $text = Get-Content .\onlykey-backup-2023-10-11T21-13.txt -Raw 
> PS C:> Set-Clipboard -Value $text
> ```
>
> Then you can paste the content of the file `onlykey-backup-2023-10-11T21-13.txt` in any application (`[Ctrl]+[V]`).

## Restore

Online documentation: [Restore From Backup](https://docs.onlykey.io/usersguide.html#restore-from-backup)

* Insert the key into the USB slot.
* Enter the PIN (using the 6 keys on the device). The LED is green.
* Open the desktop application.
* Select the tab `Backup/Restore`.
* Hold the #6 button down on your OnlyKey for 5+ seconds and release.
  The light turns off. Then (re)enter the PIN (using the 6 keys on the device). You will see the OnlyKey LED fade in and out continuously (Red) while in config mode.
* Select the file that contains the backup to restore.
* Click on the button "Restore the OnlyKey".  
* Wait until the operation ends.

> When the operation completes, the key reboots and you must (re)enter the PIN.

