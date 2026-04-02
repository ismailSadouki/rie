**PAM** has the potential to seriously alter the security of your Linux system, For instance, an accidental deletion of a configuration file(s) under _**/etc/pam.d/***_ or  _**/etc/pam.conf**_ can lock you out of your own system! or even can compromise the security of your whole system, if **PAM** is vulnerable, then the whole system is vulnerable, make any changes with care.

Almost all of the major modules and configuration files with **PAM** have their own manpages, and each application or service supporting **PAM** will have a corresponding configuration file in the _**/etc/pam.d/**_ directory,  each one of these files will make a reference to PAM modules (shared modules), For example, Let’s have a quick look at a version of the file **_/etc/pam.d/su-l_** :
	`> #%PAM-1.0
> 
>	` auth         include           su`
> 
> 	`account      include            su`
> 
> 	`password     include            su`
> 
> 	`session      optional           pam_keyinit.so force revoke`
> 
	 `session      include             su`

The **file** is made up of a list of rules written on a single line ( comments are preceded with "**#**" makers).

>	`module_interface control-flag module module-arguments`

where :
- **module interface:**  module type.
- **control-flag:** determine how important the success or failure of a particular module.
- **module:** the absolute filename or relative pathname of the PAM module.
- **module-arguments:** space-separated list of tokens for controlling module behavior.

**PAM module interfaces:**  

- **auth**: identifies modules used during authentication. For example, it requests and verifies the validity of a password.
- **account**:  provide services for account verification. For example, is this user valid for this type of login?
- **password**:  changing user passwords only.
- **session**: manage actions performed at the beginning of a session and end of a session. For example, mounting a user's home directory.

**PAM CONTROL FLAGS:**

- **requisite**: the module must return PAM_SUCCESS for access to be allowed, if access is denied, policy processing stops, and this control can leak info.
- **sufficient**: never reject, only says yes (failure of this module is ignored).
- **required**: the module must return PAM_SUCCESS for access to be allowed, if the test fails at this point, the user is not notified until the results of all modules tests reference that interface is complete.
- **optional**: the success or failure of this module is generally not recorded.

_**important!** The order in which the modules are listed is very important to the authentication process._

**PAM modules** 

There are many modules for PAM, Here are the most common ones:

`pam_unix` : allows you to manage the global authentication policy.

`pam_nologin`: disables all accounts except root.


### Wrap Up
By now you should have a much better idea of what PAM can do, However, be very careful with any changes you make to PAM modules. You could lock yourself out of your system, or worse, let everyone else in.