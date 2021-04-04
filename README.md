
# Disable Firefox auto filling forms

[https://support.mozilla.org/en-US/kb/autofill-logins-firefox](https://support.mozilla.org/en-US/kb/autofill-logins-firefox)

# Configuring GitHub account

Follow the instructions here: [Configuring two-factor authentication](https://docs.github.com/en/github/authenticating-to-github/configuring-two-factor-authentication#configuring-two-factor-authentication-using-fido-u2f)

First you need to configure two-factor authentication using a TOTP. To do that, install [Twilio Authy 2-Factor Authentication application](https://authy.com/download/) on your smartphone or your desktop computer.

Then configure a security key (such as the OnlyKey), on the [GitHub Settings page](https://github.com/settings/two_factor_authentication/configure).

> **WARNING**: by the 4th of april 2021, the procedure does not work with FireFox (version `87.0` - 64 bits) on Windows 10. It works with Edge (version `89.0.774.68`). See [this post](https://onlykey.discourse.group/t/github-security-key-fido2-webauthn-fails-on-windows-10/143?u=denis.beurive) for details.

**Step 1**

Connect the OnlyKey on the computer and unlock it.

**Step 2**

On the section "Security keys" ([here](https://github.com/settings/two_factor_authentication/configure)), enter a name for the key (for example "OnlyKey") and click on the "Add" button.

![](images/settings-security-keys.png)

**Step 3**

The led on the OnlyKey blinks blue.

**Step 4**

When asked to perform an action on the device, just touch one button on the OnlyKey.

That's it.


