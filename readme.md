# Media Temple

We're using a VPS (virtual private server) at Media Temple to setup a simple Linux box.

1. Setup a (ve) Server. http://mediatemple.net/webhosting/ve/
2. Associate your domain, like `vrtgo.cc`, in their DNS configuration.
3. Set the OS to Ubuntu Lucid (the default recommendation).
4. Enable SSH and change the root password.

	`
	first line of a code block
	second line
	`


## Add Users
1. Login as root and add a new user. From Mac OS Terminal:

	ssh root@vrtgo.cc
	root@vrtgo.cc's password: ****

Add a new user named `scott`, and give this account `su` privileges with `visudo`

```
% adduser scott
% visudo
```

You're running the `nano` editor. Add the following line below the root user:

```
scott	ALL=(ALL) ALL
```

Save and quit. Log out of `root` and reconnect as your new account.

## Install GIT

```
% sudo apt-get update
% sudo apt-get install git-core
% git version
```
I had to logout at this point to have git show up in the file system. I know there's a command here to fix that. 

## Change shell to tcsh
I don't like using bash for interactive shell. I'm a c-shell guy.

```csh
% chsh -s "/bin/tcsh"
```

## Generate SSH keys
## Install Ruby
## Install dotfiles


***

End of file


