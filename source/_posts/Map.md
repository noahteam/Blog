title: 'Remote usability testing tool: Click Heat Map'
date: 2015-07-19 12:04:07
tags: [Remote usability testing tool, Heat Map]
---
## About
Wanting to know the behaviour of users using a site, many will think of Click Heat Map.
Click Heat Map is a visualised click action of user done in a page with different colors.
![Click Heat Map](http://blog.corunet.com/uploads/final.png "Click Heat Map")

The basic idea of the implementation:
1. Record all the coordinates of any click action using javascript onclick event handler/ mouseclick event handler.
2. Then sending the information like coordinates, url to server and stored those information.
3. Display those stored information in heat map style.

Sample code from [stackoverflow](http://stackoverflow.com/questions/2496267/recording-user-data-for-heatmap-with-javascript)
```bash
document.onclick = function(e){
  var trackImg = new Image();
  trackImg.src = 'http://tracking.server/img.gif?x='+e.clientX+'&y='+e.clientY;
}
 ```
 
Technical difficulties:
1. Different screen size and resolution
2. Dynamic layout like comments,  pop up alert.
3. Optimise performance of recording click actions to avoid extra loading to website
4. Need to support cross-browser compatibility

If writing one is too much of work, [here](http://www.paulolyslager.com/heatmap-hot-or-not/) has a list of 3rd party application on click heat map.

Other alternative:
In-Page Analytics
![In-Page_Analytics](https://lh3.googleusercontent.com/-Qx4GZbibbtY/Va0wbWoazJI/AAAAAAAAAUc/kwzlqAJSYEo/w1009-h520-no/In-Page_Analytics.png "In-Page_Analytics")



