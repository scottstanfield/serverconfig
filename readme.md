Media Temple
============

We're using a VPS (virtual private server) at Media Temple to setup a simple Linux box.

1. Setup a (ve) Server. [(me)][2]
2. Associate your domain, like `vrtgo.cc`, in their DNS configuration.
3. Set the OS to Ubuntu Lucid (the default recommendation).
4. Enable SSH and change the root password.

I put a [bundle of bit.ly links][1] together for more info.

[1]: http://bit.ly/bundles/vertigo/1  
[2]: http://mediatemple.net/webhosting/ve/
[3]: http://vpsbible.com/security/harden-ssh-create-firewall/

Update Software
---------------
The first four shell actions (as root) will update the Ubuntu release, with the
exception of the `ifupdown` package, which causes the server network config to
not "sync" with the OpenVZ manager. (An attempt at a fix was made by user "twb"
here: http://paste.debian.net/108679, but it didn't work).


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

Save and `exit` back to Mac OS X Terminal.

		# exit


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


Harden VPS ssh
------------------
You can still `su` as root, but we need to remove this big security hole.

		$ sudo vi /etc/ssh/sshd_config
	
Look for, and change the following lines:
	
		Port 54321 # Change from port 22 to something above 22 (but not 54321)
		PermitRootLogin no
		PasswordAuthentication no
		X11Forwarding no
		UsePAM no
		UseDNS no
		AllowUsers bob alice mary

Save the file and restart the SSH Server

		$ sudo /etc/init.d/ssh restart
		$ exit

Tell your Mac SSH about the new default port

		> touch ~/.ssh/config
		> echo "Port 54321" >> ~/.ssh/config
		> ssh scott@vrtgo.cc


Setup Shell 
-----------
We install GIT right away in order to pull down the .cshrc and .vimrc files.

		$ sudo aptitude install git-core
		$ git version

I had to logout at this point to have git show up in the file system. I know there's a command here to fix that. 

### Change shell to tcsh
I don't like using bash for interactive shell. I'm a c-shell guy.

		$ chsh -s "/bin/tcsh"


### Install Ruby

Install minimal set of Ruby files in order to install our .dotfiles

		% sudo apt-get install --no-install-recommends ruby-full

### Install dotfiles

		% cd ~
		% git clone git://github.com/rm8t/dotfiles.git .dotfiles
		% cd .dotfiles
		% ./install

Firewall
--------
We're using a custom `iptables` configuration. The directions are at [vpsbible.com][3]. You need a username and password to access the site. I'll put the details in here later.

Note: I previously had notes on configuring `ufw` but never got it to work reliably. It really doesn't like to work with OpenVZ on Ubuntu. 
You can follow the steps at http://blog.bodhizazen.net/linux/how-to-use-ufw-in-openvz-templates


Web Server (nginx)
------------------
Using nginx (a static, fast web server)

		% sudo aptitude update
		% sudo aptitude intall nginx
		% sudo /etc/ini/d/nginx start
		% lynx localhost

Change `server_name` entry to your-domain.com (from localhost).

		% sudo vi /etc/nginx/sites-available/default
		% lynx your-domain.com
		

vim
---
TODO: finish this next
Check out the [VIM setup bundles][5]

[5]: http://bit.ly/bundles/vertigo/4




TODO
----
Setup email
Install nginx and django
Install `fail2ban`
Install webmin on ubuntu 10.04


End of file


