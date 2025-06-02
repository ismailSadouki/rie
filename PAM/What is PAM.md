##### How to configure and use PAM in Linux

**PAM** is an abbreviation for **Pluggable Authentication Modules**, which is the system under **GNU/Linux** that allows many applications or services to authenticate users.
	A **pluggable authentication module** (**PAM**) is a mechanism to integrate multiple low-level [authentication](https://en.wikipedia.org/wiki/Authentication "Authentication") schemes into a high-level [application programming interface](https://en.wikipedia.org/wiki/Application_programming_interface "Application programming interface") (API). PAM allows programs that rely on authentication to be written independently of the underlying authentication scheme.[wikipedia](https://en.wikipedia.org/wiki/Pluggable_authentication_module)
authentication is the phase in which it is verified that you are the person you claim to be or in other words "Is this user who they say they are?".

each program has its own way of authenticating users, and many programs are configured to use a centralized authentication mechanism called **PAM.**

**PAM** is a collection of modules that provides a single, fully-documented library that allows developers to write programs without having to create their own authentication schemes, makes the lives of administrators much easier, since we only have to learn **PAM**, instead of learning and trying to remember a separate configuration model for every system we decide to run.

https://upload.wikimedia.org/wikipedia/commons/0/01/PAM_Diagramm.svg

most administrators do not learn about **PAM** configuration files until they get involved in advanced authentication and security topics.