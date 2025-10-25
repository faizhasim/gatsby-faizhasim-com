---
title: "How to recover admin password for Streamyx (Modem DSL-2640B)"
date: "2012-11-30T17:28:42"
template: "post"
draft: false
slug: "/posts/how-to-recover-admin-password-for-streamyx-modem-dsl-2640b"
category: "Software"
tags:
  - modem
description: "Last month, I've changed the admin password for this modem. Originally it was username: `tmadmin` and password: `tmadmin`. This is the unrestricted account."
---

Last month, I've changed the admin password for this modem. Originally it was username: `tmadmin` and password: `tmadmin`. This is the unrestricted account.

And now, I want to reboot the modem and I forgot that password! I've tried to login using tmuser but there's no option to reboot the modem.

Cut to the chase, with just some HTML debugging using Chrome debugger, I found out that this modem wasn't designed to be secured at all. The password is in plain text and the validation was done purely on the UI level.

Just go to http://192.168.1.1, and log in using as any user (`tmadmin`, `tmuser` or `support`). Then go to http://192.168.1.1/password.html. View the source and you'll see something like this:

    <script language="javascript">
    <!-- hide
    pwdAdmin = '12345678';
    pwdSupport = 'support';
    pwdUser = 'tmuser';

So, for this case, now I know that "tmadmin" password is 12345678.

So, moral of story is: Even though you have changed password for the `tmadmin` user, it's not enough. You will need to change the password for non-admin users such as `tmuser` or `support`, because this modem security is absolute joke.
