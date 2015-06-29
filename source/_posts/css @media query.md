title: Introduction to CSS3 @media query
subtitle: By Zhang Guoqiang
date: 2015-06-29 21:59:15
tags: git
---
## About
For responsive design, sometimes we need to apply different style for specific media device or screen size.
By using CSS3 @media query, the mission will be quite easy to accomplish.

## Syntax
First let's have a look at the syntax

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
When the media attribute expression in <link> tag is false, the stylesheet will not be applied to the document.(But it will still be downloaded.)

In the stylesheet we can also put css style under @media query to determin when should apply.

The detailed syntax can be expressed as below

''media_query_list: <media_query> [, <media_query> ]*
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
  | scan | grid''



 




