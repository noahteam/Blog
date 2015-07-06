title: Introduction to Zen Coding
author: Zhang Guoqiang
date: 2015-07-06 22:27:22
tags: html
---

##About
During web development, it is very often we come up with an html skeleton with some css class decoration first as a template. You may find it be very tedious typing all those html tags and classes. Even though some editors provide a little helper functions like auto html tag closing, it is still a lot of typing expecially when using some predefined classes like bootstrap. By using Zen Coding tool, the process can become much easier and have a lot of fun as well.

##Syntax
For creating a navigation menu, we need to generate html like below.
```html
<div id="page">
        <div class="logo"></div>
        <ul id="navigation">
                <li><a href=""></a></li>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
                <li><a href=""></a></li>
        </ul>
</div>
```
Of course, you can type all the tags and class, copy paste the duplicated part several times and feel the effort spent is still fine. But if using Zen syntax, one line will do the trick.
```css
div#page>div.logo+ul#navigation>li*5>a
```
By using the css selector we could simply generate the same well formated html with much less typing. And the syntax is also quite straight forward.

##Zen Coding in Visual Studio
Zen Coding is shipped together with visual studio Web Essential plugin. This plugin also contains many other helpful functions which is not the focus of this article. If you have interest, could have a further reading [here](http://vswebessentials.com/).

##References
[https://code.google.com/p/zen-coding/](https://code.google.com/p/zen-coding/)
[http://docs.emmet.io/cheat-sheet/](http://docs.emmet.io/cheat-sheet/)
[http://emmet.io/](http://emmet.io/)
