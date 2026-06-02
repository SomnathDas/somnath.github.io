---
title: "new-housing-portal(web) - write-up - LACTF-2024"
date: 2024-02-19T17:09:33.189Z
draft: false
slug: "new-housing-portal-web-write-up-lactf-2024"
description: "new-housing-portal (web)-write-up— LACTF-2024 A write-up on “new-housing-portal” by r2uwu2 challenge featured in LACTF-2024 in the category of “web”. We begin by statically analyzing the..."
ogImageFromThumbnail: false
images:
  - image-1.png
---
A write-up on “new-housing-portal” by _r2uwu2_ challenge featured in LACTF-2024 in the category of “web”.

![Thumbnail for this article](image-1.png)

![/login/ page](image-2.png)

## Analysis
We begin by statically analyzing the source code provided to us.

![server.js](image-3.png)

Our goal is to get the flag which “_samy_” named _user_ holds for us. Notice that variable “_deepestDarkestSecret_” is holding this value for us. Now, we must find where is this value being used.

![server.js](image-4.png)

Also notice, when we “register” ourselves on the website, we get assigned a explicitly defined string literal of “_todo_” in our “_deepestDarkestSecret_” member variable. This means that we **do not control** this value. We only control our **username**, **name** and **password**.

![server.js](image-5.png)

Also notice that “_auth_” cookie that is being stored has set “_httpOnly_” flag to be _true_. MDN describes the flag as follows:

![https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie](image-6.png)

![server.js](image-7.png)

Our first encounter with the usage of “_deepestDarkestSecret_” value can be seen in this endpoint. `/finder` endpoint is used to “_invite_” other “_user_” through their “_username_”.

`Person A` invites `Person B` means `Person B` will get to see `Person A` ‘s `deepestDarkestSecret` value. Let’s find where we actually see this value.

![./request/index.js](image-8.png)

In the `/request` part of the website, we see “_username_” and “_deepestDarkestSecret_” of the “_Invitee_” i.e `the person who invited` .

![/request](image-9.png)

Let’s say we have two users “_fax_” and “_wax_”. Now, “_fax_” **will invite** “_wax_”.

![/finder/?q=fax](image-10.png)

Once we hit the “_invite_” button, user “_fax_” will have an invite from “wax” in their `/request` part of the website.

![/request](image-11.png)

From here it becomes clear that to get _flag_, that is to get `deepestDarkestSecret` of our victim `samy` , **we must somehow make** `samy` **user invite us.** _Cross-site-scripting_ aka `XSS` enters into the picture.

I’ve been dropping every bit of information that we need in-order to execute this attack up until now.

Let’s think about the interaction between `What we control?` and `What we see?` . Think for a bit. Re-read this article if you are still unsure.

We control `username` , `name` and we see both in the request to `/finder/?q=username` .

To test and see if we can execute `javascript` through the mentioned parameter on the `/finder` page, lets try the following.

1.  Register an account with the `username` of `dragon <img src=x onerror=alert(1337)` and a normal account.
2.  Using that account, send an `invite` to the `normal` account.
3.  Visit `/finder/?q=dragon <img src=x onerror=alert(1337)` using the normal account.

![Registering an account [ Burp-Suite Tool ]](image-12.png)

![/finder](image-13.png)

Boom! We now know that there is a `XSS` vulnerability out in the wild.

## Exploitation
Now, instead of “alerting” our `samy` , we have to `auto-click` into that `INVITE` button and send our _user_ an invite and get his `deepestDarkestSecret` .

We can select the button as follows and then use `click()` method to simulate “click on the button” and submit our _invite._

![Selecting the button using Javascript](image-14.png)

Repeat the same steps as above and check your `malicious` user-account, it will have the `deepestDarkestSecret` of the `target` user.

![Registering an account [ Burp-Suite Tool ] [2]](image-15.png)

![Automatically invited ourselves from other user’s account](image-16.png)

And **we have our payload ready to be sent** to the `admin bot` provided to us by them which will make a bot with account of `samy` visit the `link` provided by us.

![Baiting “samy” with our XSS Infected link](image-17.png)

![We got ourselves an invite from “samy”](image-18.png)

**Congratulations! We solved this challenge.**

I hope you enjoyed my courtesy for this _write-up_. If you’ve already solved this, let me know what was your approach. Thank you!

🍃 Happy Hacking!