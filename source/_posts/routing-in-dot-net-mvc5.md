title: Routing in .Net MVC5
date: 2015-07-26 23:13:17
author: Toh Leng Wee
tags: [MVC5, .Net, Routing]
---

## About
![MVC Routing](https://lh3.googleusercontent.com/gI1hs7q9a0Cp4pa4hjuwW7CzTXg2GfGjUmCBv4xXX9g=w879-h414-no "MVC Routing")
ASP.NET MVC store all the routing rules in a **RouteTable.Routes** static object. When a HTTP request sent from browser, .NET will find a match routing rule in **RouteTable.Routes**, to decide whether it should be handled by MvcHandler or other HttpHandler.
In this post, we will introduce how routing works in .NET MVC.

## RouteConfig.cs
In .NET MVC5, **RouteConfig.cs** is inside **App_Start** folder (.NET MVC4 and older version, URL Routing is registered in **Global.asax**).

```csharp
public static void RegisterRoutes (RouteCollection routes)
{
    routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );
}
```

## IgnoreRoute
Line3: **IgnoreRoute** is used for skipping matched URL patterns, in order to prevent **MvcHandler** to handle something not belongs to ASP.NET MVC. In above example, it will ignore all the **.axd** request. {resource} and {pathInfo} are something similar with **{0}** in **String.Format**. They will be replaced with a real URL. Noted there's a ***** in {pathInfo} which mean include the whole URL. 
For example: **test.asd/detail/sports/3**
{pathInfo} you will get **detail**
{\*pathInfo} you will get **detail/sports/3**

## MapRoute
MapRoute parameters used [Named and Optional Arguments](https://msdn.microsoft.com/en-us/library/dd264739.aspx "Named and Optional Arguments") which introduced in C# 4.0.
Line6: **name** must be unique.
Line7: **url** define URL Routing patterns, you could define your own pattern here.
Line8: **default** when the URL does not match any patterns that defined before default, then it will use default pattern. Note that the **id** is set to **UrlParameter.Optional** which mean id is optional in URL parameters.

## Add a self define URL Routing
For example, we have a controller called **Home** and there are 2 actions under **Home** which are **About** and **Contact**. In order to hide **Home** from URL, we can add a self define URL Routing.

| URL before adding Test        |URL after adding Test           |
| ----------------------------- |:------------------------------:|
| http://localhost/Home/About   |http://localhost/About          |
| http://localhost/Home/Contact |http://localhost/Contact        |

```csharp
public static void RegisterRoutes (RouteCollection routes)
{
    routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

    routes.MapRoute(
        name: "Test",
        url: "{action}/{id}", //remove {controller}
        defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );

    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );
}
```

## Note
Please note that not all routes are created equally. Routes have priority defined by the order of appearance of the routes in the **RouteConfig.cs** file. The priority goes from **top to bottom**. The last route in that file is at the lowest priority and will be applied last. If no route matches, 404 is returned.