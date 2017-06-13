---
author: azizuysal
comments: true
date: 2011-03-06 17:34:00+00:00
layout: post
link: https://azizuysal.wordpress.com/2011/03/06/adding-a-background-image-to-andor-changing-the-tint-color-of-the-iphone-tab-bar-uitabbar/
slug: adding-a-background-image-to-andor-changing-the-tint-color-of-the-iphone-tab-bar-uitabbar
title: Adding a Background Image to and/or Changing the Tint Color of the iPhone Tab
  Bar (UITabBar)
wordpress_id: 129
categories:
- Software
tags:
- iphone
---

![iPhone Screenshot](http://azizuysal.files.wordpress.com/2011/03/tabbartint.png)





The default UITabBar on iPhone has a black translucent tint which is suitable for most UI designs. However, there my be some times when you want to change the tint color, or even add a background image or pattern to the tab bar, to better suit your UI design. Here is how to accomplish this simple task:





You can change the tint color of the tab bar as follows:  

```objc
CGFloat width = self.tabBarController.tabBar.bounds.size.width;
CGFloat height = self.tabBarController.tabBar.bounds.size.height;
UIView *tabBarTint = [[UIView alloc] initWithFrame:CGRectMake(0.0, 0.0, width, height)];
[tabBarTint setBackgroundColor:[UIColor blueColor]];
[self.tabBarController.tabBar insertSubview:tabBarTint atIndex:0];
[tabBarTint release];
```





The trick is to insert the subview at index 0, behind the actual view of the tab bar. Optionally, you can try changing the alpha property of tabBarTint object to fine tune the color effect you want.





With a slight variation of the above code, you can also add a pattern or a background image to the tab bar to have a more dramatic effect, as follows:  

```objc
UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"concave.jpg"]];
[self.tabBarController.tabBar insertSubview:imageView atIndex:0];
[imageView release];
```





Here, I created a UIImageView, which is a subclass of UIView, and inserted that at index 0, instead of the generic UIView in the previous example. The result is the same, the tab bar now has a background image. It is also possible to combine the two methods shown above in order to add a background image and change its tint at the same time.





You can find a sample project implementing the above code [here](http://www.box.net/shared/myxl5ff2dn).
