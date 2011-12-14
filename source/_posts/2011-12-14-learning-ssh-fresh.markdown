---
layout: post
title: "SSH入门"
date: 2011-12-14 07:45
comments: true
categories: ssh
---



Linux OpenSSH 初级笔记

节选自[SSH_Tutorial_for_Linux](http://support.suso.com/supki/SSH_Tutorial_for_Linux)
##定义及特性

SSH, which is an acronym for Secure SHell, was designed and created to provide the best security when accessing another computer remotely. Not only does it encrypt the session, it also provides better authentication facilities, as well as features like secure file transfer, X session forwarding, port forwarding and more so that you can increase the security of other protocols. It can use different forms of encryption ranging anywhere from 512 bit on up to as high as 32768 bits and includes ciphers like AES (Advanced Encryption Scheme), Triple DES, Blowfish, CAST128 or Arcfour. Of course, the higher the bits, the longer it will take to generate and use keys as well as the longer it will take to pass data over the connection. 

##从登录开始

By default ssh will assume that you want to authenticate as the same user you use on your local machine. To override this and use a different user, simply use `remoteusername@hostname` as the argument. Such as in this example:

`ssh username@username.suso.org`

The first time around it will ask you if you wish to add the remote host to a list of known_hosts, go ahead and say yes.

{% codeblock %}
The authenticity of host 'arvo.suso.org (216.9.132.134)' can't be established.
RSA key fingerprint is 53:b4:ad:c8:51:17:99:4b:c9:08:ac:c1:b6:05:71:9b.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'arvo.suso.org' (RSA) to the list of known hosts.
{% endcodeblock %}

It is important to pay attention to this question however because this is one of SSH's major features. **Host validation**. To put it simply, ssh will check to make sure that you are connecting to the host that you think you are connecting to. That way if someone tries to trick you into logging into their machine instead so that they can sniff your SSH session.After saying yes, it will prompt you for your password on the remote system.

Now that you have spent all that time reading and are now connected, go ahead and logout. ;-) Once you're back to your local computer's command prompt enter the command `ssh-keygen -t rsa`.

It should begin spitting out the following:

{% codeblock %}
Generating public/private dsa key pair.
Enter file in which to save the key (/home/localuser/.ssh/id_rsa): 
	Enter passphrase (empty for no passphrase): 
		Enter same passphrase again: 
		Your identification has been saved in /home/localuser/.ssh/id_rsa.
		Your public key has been saved in /home/localuser/.ssh/id_rsa.pub.
		The key fingerprint is:
		93:58:20:56:72:d7:bd:14:86:9f:42:aa:82:3d:f8:e5 localuser@mybox.home.com
{% endcodeblock %}

Next it will ask you for a passphrase and ask you to confirm it. The idea behind what you should use for a passphrase is different from that of a password. Ideally, you should choose something unique and unguessable, just like your password, but it should probably be something much longer, like a whole sentence. 

Whenever you connect via ssh to a host that has your public key loaded in the authorized_keys file, it will use a challenge response type of authentication which uses your private key and public key to determine if you should be granted access to that computer It will ask you for your key passphrase though. But this is your local ssh process that is asking for your passphrase, not the ssh server on the remote side. It is asking to authenticate you according to data in your private key.** Using key based authentication instead of system password authentication may not seem like much of a gain at first, but there are other benefits that will be explained later, such as logging in automatically from X windows**.

Go ahead and copy your public key which is in ~/.ssh/id_dsa.pub to the remote machine.

`scp ~/.ssh/id_rsa.pub username@arvo.suso.org:.ssh/authorized_keys`

It will ask you for your system password on the remote machine and after authenticating it will transfer the file. You may have to create the .ssh directory in your home directory on the remote machine first.

Now when ssh to the remote machine, it should ask you for your key passphrase instead of your password. **If it doesn't, it could be that the permissions and mode of the authorized_keys file and .ssh directory on the remote server need to be set more restrictively**. You can do that with these commands on the remote server:

{% codeblock %}
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
{% endcodeblock %}

You can also put the public key in the remote authorized_keys file by **simply copying it into your paste buffer, logging into the remote machine and pasting it directly into the file **from an editor like vi, emacs or nano. I would recommend using the 'cat' program to view the contents of the public key file though because using less will end up breaking the single line into multiple lines.

`cat ~/.ssh/id_rsa.pub`

A newer way that you can quite easily install your public ssh key on a remote host is with the ssh-copy-id program like this:

ssh-copy-id yourusername@your.website.com

##使用ssh-agent

The true usefulness of using key based authentication comes in the use of the ssh-agent program.
 
允许不提供密码就建立连接，而且还使专用密钥可以在磁盘上保持加密状态。keychain是一个非常方便的 ssh-agent 前端，可以使 ssh-agent 比以前更可靠、更方便而且使用起来更具趣味性。



##X会话转发

One lesser known feature of X windows is its network transparency. It was designed to be able to transmit window and bitmap information over a network connection. So essentially you can login to a remote desktop machine and run some X windows program like Gnumeric, Gimp or even Firefox and the program will **run on the remote computer, but will display its graphical output on your local computer.**

**To try this out, you will need an account on a remote computer that has X windows installed with some X windows applications.** The key to making it work is using the -X option, which means "forward the X connection through the SSH connection". This is a form of tunneling.

`ssh -X username@desktopmachine.domain.com`

If this doesn't work, you may have to setup the SSH daemon on the remote computer to allow X11Forwarding, check that the following lines are set in /etc/ssh/sshd_config on that computer:

{% codeblock %}
X11Forwarding yes
X11DisplayOffset 10
X11UseLocalhost yes
{% endcodeblock %}

**For some newer programs and newer versions of X windows, you may need to use the -Y option instead for trusted X11 forwarding. **Try using this option if your X11 windows program fails to start running with a message like this one that was for Gimp:

{% codeblock %}
The program 'gimp-2.2' received an X Window System error.
This probably reflects a bug in the program.
The error was 'BadWindow (invalid Window parameter)'.
(Details: serial 154 error_code 3 request_code 38 minor_code 0)
	(Note to programmers: normally, X errors are reported asynchronously;
	 that is, you will receive the error a while after causing it.
	 To debug your program, run it with the --sync command line
	 option to change this behavior. You can then get a meaningful
	 backtrace from your debugger if you break on the gdk_x_error()
	 function.)
{% endcodeblock %}

##TCP端口转发

Like X11 session forwarding, **SSH can also forward other TCP application level ports both forward and backwards across the SSH session that you establish**.

For example, you can setup a port forward for your connection from your home machine to arvo.suso.org so that it will take connections to localhost port 3306 and forward them to the remote side mysql.suso.org port 3306. **Port 3306 is the port that the MySQL server listens on, so this would allow you to bypass the normal host checks that the MySQL server would make and allow you to run GUI MySQL programs on your local computer while using the database on your suso account. Here is the command to accomplish this:**

`ssh -L 3306:mysql.suso.org:3306 username@arvo.suso.org`

The -L (which means Local port) takes one argument of

`<local-port>:<connect-to-host>:<connect-to-port>`

so you specify what host and port the connection will go to on the other side of the SSH connection. When you make a connection to the <local-port> port, it sends the data through the SSH connection and then connects to <connect-to-host>:<connect-to-port> on the other side. From the point of view of <connect-to-host>, its as if the connection came from the SSH server that you login to. In the case above, arvo.suso.org.

This is much like a VPN connection allows you to act like you are making connections from the remote network that you VPN into.

**Take a moment to think of other useful connections you can make with this type of network tunnel.**

Another useful one is for when you are away from home and can't send mail through your home ISP's mail server because it only allows local connections to block spam. You can create an SSH tunnel to an SSH server that is local to your ISP and then have your GUI mail client like Thunderbird make a connection to localhost port 8025 to send the mail. Here is the command to create the tunnel:

`ssh -L 8025:smtp.homeisp.net:25 username@shell.homeisp.net`

One thing to note is that **non-root users do not normally have the ability to listen on network ports lower than 1024, so listening on port 25 would not work, thus we use 8025. It really doesn't matter**, you can use any port as long as your email client can connect to it.

You can also reverse the direction and create a reverse port forward. This can be useful if you want to connect to a machine remotely to allow connections back in. For instance, I use this sometimes so that I can create a reverse port 22 (SSH) tunnel so that I can reconnect through SSH to a machine that is behind a firewall once I have gone away from that network.

ssh -R 8022:localhost:22 username@my.home.ip.address

This will connect to my home machine and start listening on port 8022 there. Once I get home, I can then connect back to the machine I created the connection from using the following command:

ssh -p 8022 username@localhost

Remember to use the right username for the machine that you started the tunnel from. It can get confusing. You also have to keep in mind that since you are connecting to the host called localhost, but its really a port going to a different SSH server, you may wind up with a different host key for localhost the next time you connect to localhost. In that case you would need to edit your .ssh/known_hosts file to remove the localhost line. You really should know more about SSH before doing this blindly.

As a final exercise, you can keep your reverse port forward open all the time by starting the connection with this loop:

while true ; do ssh -R 8022:localhost:22 suso@my.home.ip.address ; sleep 60 ; done

This way, if you happen to reboot your home machine, the reverse tunnel will try to reconnect after 60 seconds. Provided you've setup keys and your ssh-agent on the remote machine. ;-)


##SOCKS5代理

So thats great and all, but eventually you are going to want to know how you can do tunneling without having to specify the address that you want to forward to.

This is accomplished through the -D SOCKS5 option.

ssh -D 9999 username@remotehost.net

Any application that supports the SOCKS5 protocol (and most of the big network programs do) can forward its network connection over SSH and dynamically forward to any hostname that you specify. So for a web browser, any URL that you type in the URL field, would be sent through the SSH tunnel. Firefox, Xchat, Gaim and many others all support using SOCKS5. The setting is usually under preferences in the connection settings.

Remember, in the words of Benjamin "Uncle Ben" Parker, with great power comes great responsibility. Just because you can get around firewalls and use other hosts for sending network traffic, doesn't mean that some system administrator isn't going to notice you.


##通过使用简单指令

Sometimes you don't really want to run a shell like Bash on the host you are connecting to. Maybe you just want to run a command and exit. This is very simply accomplished by putting the command you wish to run at the end of your ssh connection command.

`ssh username@remotehost.net ls -l /`

You can also do something called forced-command where you force any login attempt to run a specific command regardless of what is specified on the command line by the client.

To do this, you put this variable and the command you want to force in the authorized_keys file on the remote host:

command="/usr/bin/backup" ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAIEAvna.....

Put the variable before the start of the line for the key. There are other variables you can use here like from="" to allow only from a specific host. These variables can be put together separated by commas.

##使用SCP

SCP is basically a program that uses the SSH protocol to send files between hosts over and encrypted connection. You can transfer files from your local computer to a remote host or vice versa or even from a remote host to another remote host.

Here is a basic command that copies a file called report.doc from the local computer to a file by the same name on the remote computer.

`scp report.doc username@remote.host.net:`

Note how the lack of a destination filename just preserves the original name of the file. This is also the case if the remote destination includes the path to a directory on the remote host.

To copy the file back from the server, you just reverse the from and to.

`scp username@remote.host.net:report.doc report.doc`

If you want to specify a new name for the file on the remote computer, simply give the name after the colon on the to side.

`scp report.doc username@remote.host.net:monday.doc`

Or if you want to copy it to a directory relative to the home directory for the remote user specified.

`scp report.doc username@remote.host.net:reports/monday.doc`

You can also use fullpaths which are preceded with a /.

To copy a whole directory recursively to a remote location, use the -r option. The following command copies a directory named mail to the home directory of the user on the remote computer.

`scp -r mail username@remote.host.net:`

Sometimes you will want to preserve the timestamps of the files and directories and if possible, the users, groups and permissions. To do this, use the -p option.

`scp -rp mail username@remote.host.net:`


##保持SSH连接

Sometimes you may have trouble keeping your SSH session up and idle. For whatever reason, the connection just dies after X minutes of inactivity. Usually this happens because there is a firewall between you and the internet that is configured to only keep stateful connections in its memory for **15 or so minutes**.

Fortunately, in recent versions of OpenSSH, there is a fix for this problem. Simply put the following:

{% codeblock %}
Host *
Protocol 2
TCPKeepAlive yes
ServerAliveInterval 60
{% endcodeblock %}

in the file

`~/.ssh/config`

The file above can be used for any client side SSH configuration. See the ssh_config man page for more details. The 'TCPKeepAlive yes' directive tells the ssh client that it should send a little bit of data over the connection periodically to let the server know that it is still there. **'ServerAliveInterval 60' sets this time period for these messages to 60 seconds. **This tricks many firewalls that would otherwise drop the connection, to keep your connection going.

> ssh 协议的版本1使用的是RSA密钥，而DSA密钥却用于协议级2，这是ssh协议的最新版本。

扩展阅读：



[Linux上的常用文件传输方式介绍与比较](http://www.ibm.com/developerworks/cn/linux/l-cn-filetransfer/)

[使用screen管理你的远程会话](http://www.ibm.com/developerworks/cn/linux/l-cn-screen/)

[在Linux上进行自动备份：轻松进行自主、安全、分布式网络备份](http://www.ibm.com/developerworks/cn/linux/l-backup/index.html?ca=drs-)

[基于SSH的远程操作以及安全，快捷的数据传输](http://www.ibm.com/developerworks/cn/linux/l-cn-ssh/index.html)

[SSH 安全性和配置入门：动手实践指南](http://www.ibm.com/developerworks/cn/aix/library/au-sshsecurity/)

[保护SSH的三把锁：增加黑客入侵的难度](http://www.ibm.com/developerworks/cn/aix/library/au-sshlocks/)

[实战SSH端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/)

[对话UNIX，第3部分: 在命令行中完成所有的工作](http://www.ibm.com/developerworks/cn/aix/library/au-speakingunix3.html)

[操纵数据与文件：移动和管理存储在多个系统中的文件](http://www.ibm.com/developerworks/cn/aix/library/au-speakingunix5.html?ca=drs-cn)

[通过SSH密钥验证实现在不同系统之间的脚本自动化](http://www.ibm.com/developerworks/cn/aix/library/1006_lisali_sshlogon/index.html)





