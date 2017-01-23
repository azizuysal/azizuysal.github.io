---
author: azizuysal
comments: true
date: 2011-01-02 20:24:58+00:00
layout: post
link: https://azizuysal.wordpress.com/2011/01/02/redirecting-nslog-output-to-a-file-on-demand-for-iphone-debugging/
slug: redirecting-nslog-output-to-a-file-on-demand-for-iphone-debugging
title: Redirecting NSLog Output to a File on Demand for iPhone Debugging
wordpress_id: 117
categories:
- Software
tags:
- iphone
---

[NSLog](http://www.cocoadev.com/index.pl?NSLog) can be a great tool for debugging when developing iPhone applications. It outputs a formatted message to StdErr, which is the console view in Xcode by default. However, that doesn't help much when you want to collect information in a file on a real device over several hours of real world usage. Sure, you can write separate routines to log information to a file yourself, but why muddy your code with all that extra code, when, instead, you can simply redirect existing NSLog output to a file?




Whenever you want to redirect NSLog output to a file, you can do so by using a simple [freopen](http://www.cplusplus.com/reference/clibrary/cstdio/freopen/) statement to redirect StdErr to a file, like this:




[sourcecode language="objc" light="true" wraplines="false"]
NSString *cachesDirectory = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) objectAtIndex:0];
NSString *logPath = [cachesDirectory stringByAppendingPathComponent:@"application.log"];
freopen([logPath cStringUsingEncoding:NSASCIIStringEncoding], "a+", stderr);
[/sourcecode]




The above statements will cause all output for StdErr, including all NSLog output, to be redirected to a file called "application.log". The file will be created if it doesn't already exist, and the output will be appended to the file contents, if one already exists. If you use "w+" as the second parameter to the freopen call, the file will be truncated, its contents erased, each time this statement is called. Notice also that the file is created in <Application_Home>/Library/Caches folder, rather than the <Application_Home>/Documents folder. Caches folder is not backed up by iTunes in the later versions of iOS, and I think is more suitable for log files.




Please note that redirecting StdErr to a file will slow down your application, depending on how much and how often you output logging information. If you want to be able to redirect StdErr back to the console, you can do so by first saving the original StdErr like this:




[sourcecode language="objc" light="true" wraplines="false"]
savedStdErr = dup(STDERR_FILENO);
[/sourcecode]




And then, later when you want to revert output, you can do so like this:




[sourcecode language="objc" light="true" wraplines="false"]
fflush(stderr);
dup2(savedStdErr, STDERR_FILENO);
close(savedStdErr)
[/sourcecode]




You can download a sample project implementing the above ideas [here](http://www.box.net/shared/doylr5y2bz).




You can retrieve the saved file easily when you connect the device to your Mac via USB. Open Xcode, click on menu item Window -> Organizer, and click on the name of your device when the Organizer window opens. You will see a list of all applications on the device on the lower right side, titled 'Applications'. Click on the disclosure triangle to the left of your application's name, and you should see a row titled 'Application Data' and if you click on the arrow to the right, you can download all of the application data on your Mac.




![get_file_from_device.png](http://azizuysal.files.wordpress.com/2011/01/get_file_from_device.png)



