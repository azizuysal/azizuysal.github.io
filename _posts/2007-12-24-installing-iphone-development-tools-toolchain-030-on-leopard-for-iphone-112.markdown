---
author: azizuysal
comments: false
date: 2007-12-24 12:36:58+00:00
layout: post
link: https://azizuysal.wordpress.com/2007/12/24/installing-iphone-development-tools-toolchain-030-on-leopard-for-iphone-112/
slug: installing-iphone-development-tools-toolchain-030-on-leopard-for-iphone-112
title: Installing iPhone Development Tools (Toolchain 0.30) on Leopard for iPhone
  1.1.2
wordpress_id: 12
categories:
- Software
tags:
- iphone
---

  


![hello.png](http://azizuysal.files.wordpress.com/2010/12/hello.png)

  


After [jailbreaking](/2007/12/iphone-jailbreak-and-upgrade-to-112.html) my iPhone recently, I'm now ready to install the development tools. At the end of this process, I'll finally be ready to start developing my own applications for the iPhone.

I'm using a MacBook Pro with Mac OS X 10.5.1 (Leopard). My iPhone is an 8Gb model with 1.1.2 firmware which I jailbroke recently. I have already installed the Developer Tools on my system. If you didn't, now is the time to do so. Insert your Leopard DVD, open **Optional Installs** folder, open **XcodeTools** folder, double-click **XcodeTools.mpkg** to start the installation.

I consulted [iPhoneDevDocs.com site](http://idevdocs.com/tutorial.php?t=9) for installation instructions and the required files. Though the instructions are for 1.1.1 firmware, they worked, with a couple of small changes, for the 1.1.2 firmware as well.

Fire up your **Terminal.app**, you'll be using it exclusively for this installation. And finally, check your bison and flex installations, they are required for the next steps.

[sourcecode language="bash" light="true" wraplines="false"]
$ bison --version
bison (GNU Bison) 2.3
Written by Robert Corbett and Richard Stallman.

Copyright (C) 2006 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
$ flex --version
flex 2.5.33
[/sourcecode]

First, check out revision 42498 of **LLVM SVN** and build it. If the current directory is your home folder, this will create a llvm-svn folder in it. If you want to place it elsewhere, either `cd` to that directory first, or type it in the command below.

[sourcecode language="bash" light="true" wraplines="false"]
svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm-svn -r 42498
pushd llvm-svn
./configure --enable-optimized
make ENABLE_OPTIMIZED=1
sudo make install
LLVMOBJDIR=`pwd`
popd
[/sourcecode]

Next, get a copy of the **iphone-dev** from Google Code repository.

[sourcecode language="bash" light="true" wraplines="false"]
svn checkout http://iphone-dev.googlecode.com/svn/trunk/ iphone-dev
pushd iphone-dev
[/sourcecode]

The above command will create an iphone-dev folder in your home folder. Now, create a folder for the toolchain and build **odcctools**.

[sourcecode language="bash" light="true" wraplines="false"]
sudo mkdir /usr/local/arm-apple-darwin
mkdir -p build/odcctools
pushd build/odcctools
../../odcctools/configure --target=arm-apple-darwin --disable-ld64
export INCPRIVEXT="-isysroot /Developer/SDKs/MacOSX10.4u.sdk"
make
sudo make install
popd
[/sourcecode]

At this point, you need to make a copy of your **iphone's filesystem**. There are several methods for doing this, as outlined [here](http://iphone.fiveforty.net/wiki/index.php/Break_DMG_Password), [here](http://www.touchdev.net/wiki/Jailbreak_Guide) and [here](http://code.google.com/p/iphone-dev/wiki/Building). However, I discovered that using rsync is the easiest way to accomplish this.

[sourcecode language="bash" light="true" wraplines="false"]
rsync -avz --exclude=/private/var/root/Media -e ssh root@10.0.1.197:/ ~/Desktop/iPhone/
[/sourcecode]

The SSH password for root is _'alpine'_. This will copy the contents of your iPhone (except the media, i.e your songs, photos, videos, etc) to a folder called iPhone on your desktop. You must substitute your phone's ip address, which you can find out when you goto Settings->Wi-Fi and select the name of your network on your iPhone. Please note that this will only work if your iPhone is already jailbroken, and that you've installed openSSH.

When the copying is done, right-click on the iPhone folder on your desktop an select "Compress iPhone". This will create a zip file of the folder contents. Copy the zip file to a safe place in case you need it again. Also, copy the filesystem for use by the toolchain.

[sourcecode language="bash" light="true" wraplines="false"]
sudo mkdir /usr/local/share/iphone-filesystem
sudo cp -Rp ~/Desktop/iPhone/* /usr/local/share/iphone-filesystem/
[/sourcecode]

You can now delete the iPhone folder from your desktop. And type the following in your terminal:

[sourcecode language="bash" light="true" wraplines="false"]
HEAVENLY=/usr/local/share/iphone-filesystem
[/sourcecode]

Now, patch and install the **system headers**.

[sourcecode language="bash" light="true" wraplines="false"]
pushd include
./configure --with-macosx-sdk=/Developer/SDKs/MacOSX10.4u.sdk
sudo bash install-headers.sh
popd
[/sourcecode]

Next, install **csu**.

[sourcecode language="bash" light="true" wraplines="false"]
mkdir -p build/csu
pushd build/csu
../../csu/configure --host=arm-apple-darwin
sudo make install
popd
[/sourcecode]

And finally, build and install **LLVM-GCC**. Note that both $HEAVENLY and $LLVMOBJDIR must be set as per above instructions before attempting this.

[sourcecode language="bash" light="true" wraplines="false"]
mv llvm-gcc-4.0-iphone/configure llvm-gcc-4.0-iphone/configure.old
sed 's/^FLAGS_FOR_TARGET=$/FLAGS_FOR_TARGET=${FLAGS_FOR_TARGET-}/g' llvm-gcc-4.0-iphone/configure.old &gt; llvm-gcc-4.0-iphone/configure
sudo ln -s /usr/local/arm-apple-darwin/lib/crt1.o /usr/local/arm-apple-darwin/lib/crt1.10.5.o
mkdir -p build/llvm-gcc-4.0-iphone
pushd build/llvm-gcc-4.0-iphone
export FLAGS_FOR_TARGET="-mmacosx-version-min=10.1"
chmod 755 ../../llvm-gcc-4.0-iphone/configure
../../llvm-gcc-4.0-iphone/configure --enable-llvm=`llvm-config --obj-root` --enable-languages=c,c++,objc,obj-c++ --target=arm-apple-darwin --enable-sjlj-exceptions --with-heavenly=$HEAVENLY --with-as=/usr/local/bin/arm-apple-darwin-as --with-ld=/usr/local/bin/arm-apple-darwin-ld
make LLVM_VERSION_INFO=2.0-svn-iphone-dev-0.3-svn
sudo make install
popd
popd
[/sourcecode]

That's it, you're done. You now have a working iphone-dev toolchain, and you can build iPhone applications from source. To test your new environment, download a copy of the [HelloWorld](http://ellkro.jot.com/WikiHome/HelloWorldSrc-0_30.zip?cacheTime=1190196505745) application and build it.

[sourcecode language="bash" light="true" wraplines="false"]
cd ~/Desktop/HelloWorld/
make
[/sourcecode]

Congratulations! You've successfully built your first iPhone app. You can now copy the "Hello" file to your iPhone via your favorite FTP application (or you can use sftp from from the command line) and execute it using the vt-100 terminal application on the iPhone.

Stay tuned for windows installation instructions. I'll be installing the toolchain on my Windows XP machine soon.
