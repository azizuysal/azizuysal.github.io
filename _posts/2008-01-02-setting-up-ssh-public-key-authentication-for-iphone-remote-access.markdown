---
author: azizuysal
comments: true
date: 2008-01-02 07:50:11+00:00
layout: post
link: https://azizuysal.wordpress.com/2008/01/02/setting-up-ssh-public-key-authentication-for-iphone-remote-access/
slug: setting-up-ssh-public-key-authentication-for-iphone-remote-access
title: Setting Up SSH Public Key Authentication for iPhone Remote Access
wordpress_id: 17
categories:
- Software
tags:
- iphone
---

Lately, after [jailbreaking](/2007/12/iphone-jailbreak-and-upgrade-to-112.html) my iPhone and installing the [toolchain](/2007/12/installing-iphone-development-tools-toolchain-030-on-leopard-for-iphone-112.html) on my Mac, I've started doing some iPhone development. Currently, due to lack of official development tools and a device simulator, the development process involves frequently loading up the app you're coding to your iPhone. I use the scp command to copy the app bundle as shown below.

```bash
scp -r ~/iPhone/Sample/build/Release/Sample.app root@10.0.1.1:/Applications
```

This works fine, except that it asks for the root password each time you execute the command. And this additional step can become an annoying time-waster when you end up issuing the command literally hundreds of times throughout the course of a coding session. So, [SSH public key authentication](http://sial.org/howto/openssh/publickey-auth/) comes to the rescue! A quick Google search revealed the solution at [Game Over's wiki](http://wiki.iphonegameover.com/SSH_Public_Key_Authentication).

Here are the few simple steps to enable this feature (please note that you must already have the BSD subsystem and the OpenSSH apps installed on your iPhone):

1) Start up your Terminal.app and run the following command on your mac to generate the keys. Accept the defaults when it asks for a place to save the keys on your machine. Enter a passphrase when asked if you like, but it's ok if you leave it blank too.

```bash
ssh-keygen -t rsa -b 2048
```

2) Now, copy the generated public key to your iPhone. Don't forget to substitute your phone's ip address.

```bash
scp ~/.ssh/id_rsa.pub root@10.0.1.1:~/authorized_keys
```

3) Next, start the Term-vt100 app on your iPhone, and type the following commands.

```bash
mkdir -p ~/.ssh
chmod 0700 ~/.ssh
mv ~/authorized_keys ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod go-w /private/var/root
```

That's it. From now on, you can login to your iPhone without entering a password. Try it. Type this on your mac.

```bash
ssh root@10.0.1.1
```

Don't forget to substitute your own ip address. Also note that you may be asked for a passphrase if you entered one in the first step. If you check the box to store the phrase in your keychain, you will not be asked for it anymore.
