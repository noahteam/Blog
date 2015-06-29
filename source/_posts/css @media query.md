title: Introduction to CSS3 @media query
subtitle: By Zhang Guoqiang
date: 2015-06-29 21:59:15
tags: css
---
## About
For responsive design, sometimes we need to apply different style for specific media device or screen size.
By using CSS3 @media query, the mission will be quite easy to accomplish.

## Syntax
First let's have a look at where media query can be applied.

```css
<!-- CSS media query on a link element -->
<link rel="stylesheet" media="(max-width: 800px)" href="example.css" />

<!-- CSS media query within a stylesheet -->
<style>
@media (max-width: 600px) {
  .facet_sidebar {
    display: none;
  }
}
</style>
```
When the media attribute query in <link> tag is false, the stylesheet will not be applied to the document.(But it will still be downloaded.)

In the stylesheet we can also put css style under @media query to determine when should be applied.

The detailed syntax can be expressed as below

```
media_query_list: <media_query> [, <media_query> ]*
media_query: [[only | not]? <media_type> [ and <expression> ]*]
  | <expression> [ and <expression> ]*
expression: ( <media_feature> [: <value>]? )
media_type: all | aural | braille | handheld | print |
  projection | screen | tty | tv | embossed
media_feature: width | min-width | max-width
  | height | min-height | max-height
  | device-width | min-device-width | max-device-width
  | device-height | min-device-height | max-device-height
  | aspect-ratio | min-aspect-ratio | max-aspect-ratio
  | device-aspect-ratio | min-device-aspect-ratio | max-device-aspect-ratio
  | color | min-color | max-color
  | color-index | min-color-index | max-color-index
  | monochrome | min-monochrome | max-monochrome
  | resolution | min-resolution | max-resolution
  | scan | grid
 ```
 
@media should follow by a list of media queries seperated by comma. Each media query contains logic operator, media type and expression. Media expression consists of meida feature and it's value.
Media feature usually starts with min or max to indicate greater than or less than. The purpose is replacing < or > to avoid confussion in html. All parts bracketed with [] is optional.

We take below media query as example.
```
@media (min-width: 700px), handheld and (orientation: landscape) { ... }
```

The above statement contains two media queries **(min-width:700px)** and **handheld and (orientation: landscape)**
The first query **min-width:700px** consists of media feature and its value. Because media type is not specified. So it applies to all media types. 
Media feature uses min as greater than. So the whole query means for any media type if the viewport width is greater than 700px then it is true.

For the second query **handheld and (orientation: landscape)**, **handheld** is the media type for handheld devices. **and** is the logic operator which connects its before and after conditions.
**orientation: landscape** indicates whether the viewport is landcape (width is bigger than height).

So the whole query list means for all devices if viewport width is larger than 700px or for handheld devices the orientation is landscape the style will be applied.

## References
[https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Media_queries)
[https://developer.mozilla.org/en-US/docs/Web/CSS/@media](https://developer.mozilla.org/en-US/docs/Web/CSS/@media)




 




