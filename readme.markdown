Media Temple
============

We're using a VPS (virtual private server) at Media Temple to setup a simple Linux box.

1. Setup a (ve) Server. http://mediatemple.net/webhosting/ve/
2. Associate your domain, like `vrtgo.cc`, in their DNS configuration.
3. Set the OS to Ubuntu Lucid (the default recommendation).
4. Enable SSH and change the root password.

Update Software
---------------
The first four shell actions (as root) will update the Ubuntu release,
with the exception of the `ifupdown` package, which causes the server 
network config to not "sync" with the OpenVZ manager. (An attempt at a fix
was made by user "twb" here: http://paste.debian.net/108679, but it didn't work).

* Change the default location for `aptitude` files from /tmp to /var/local/tmp 

		# wget -q paste.debian.net/plain/108685 -O - | sh

* Update installation (except for `ifupdown`)

		# aptitude update
		# aptitude hold ifupdown
		# aptitude upgrade

* While not necessary, I would `reboot` just in case. Ping until back up.

		# reboot

Add Users
---------

1. Login as root and add a new user. From Mac OS Terminal:

		> ssh root@vrtgo.cc  
		> root@vrtgo.cc's password: ****  

2. Add a new user named `scott`, and give this account `su` privileges with `visudo`

		# adduser scott  
		# visudo  

3. Add the following line below the root user:

		scott	ALL=(ALL) ALL

Save and quit. Enter `exit` and reconnect as your new account.


Secure SSH with keys
--------------------
On your Mac, generate a keypair. Make sure you use a phassphrase! 

		> ssh-keygen 

Now, to transfer your client public key to the server, you need `ssh-copy-id`
which doesn't come on Mac OS X. Fortunately, there's a bash script on our 
main server, that you can simply copy down to your home folder:

		> scp scott@vrtgo.cc:/usr/bin/ssh-copy-id ssh-copy-id

Run the script to copy your keys, then log back in.

		> ssh-copy-id scott@vrtgo.cc
		> ssh scott@vrtgo.cc

If the last line worked, you were able to login *without* your password.


Disable Root Login
------------------
You can still `su` as root, but we need to remove this big security hole.

		$ sudo vi /etc/ssh/sshd_config
	
Change the PermitRootLogin to "no". There are other tweeks we can make later.
	
		PermitRootLogin no

Save the file and restart the SSH Server

		$ sudo /etc/init.d/ssh restart
	

Setup Shell 
-----------
We install GIT right away in order to pull down the .cshrc and .vimrc files.

		$ sudo aptitude install git-core
		$ git version

I had to logout at this point to have git show up in the file system. I know there's a command here to fix that. 

## Change shell to tcsh
I don't like using bash for interactive shell. I'm a c-shell guy.

		$ chsh -s "/bin/tcsh"


## Install Ruby

Install minimal set of Ruby files in order to install our .dotfiles

		% sudo apt-get install --no-install-recommends ruby-full

## Install dotfiles

		% cd ~
		% git clone git://github.com/rm8t/dotfiles.git .dotfiles
		% cd .dotfiles
		% ./install


TODO
----
Install `fail2ban`


End of file


