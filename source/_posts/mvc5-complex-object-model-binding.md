title: MVC 5 Complex Object Model Binding
author: chen chao
tags: [mvc5, asp.net]
---

#About 

ASP.NET MVC5 can auto bind to model when receive HTTP request. However, if the model is a complex object, you need to make a little bid trick when sending request parameter. This post shows how to do that.


#Model

Suppose we have a complex model as following:

```csharp
public class Person
{
    public string Id { get; set; }
    public string Name { get; set; }
    public Address HomeAddress { get; set; }

}

public class Address
{
    public string Country { get; set; }
    public string City { get; set; }
    public string Street { get; set; }
    public string Block { get; set; }
    public string Unit { get; set; }
}

```

#Controller

There is an action in controller which is supposed to update Person object passed by HTTP Post request. The code looks like following

```csharp
[HttpPost]
public ActionResult Update(Person person)
{
    if (ModelState.IsValid)
    {
        return View();
    }
}
```


#View

In order to automatically bind HTTP Post parameter to a Person object in action, we should send the paramter of inner object properties in the format of 
``` 
{property name of parent class}.{property name of nested class} 
```

So the view may look like following

``` html
<form method="POST">
    <input type="hidden" name="Id" value="some-id"/>
    <input type="text" name="Name" value="" />
    <input type="text" name="HomeAddress.Country" value="" />
    <input type="text" name="HomeAddress.Street" value="" />
    <input type="text" name="HomeAddress.Block" value="" />
    <input type="text" name="HomeAddress.Unit" value="" />
    <input type="submit" value="Update" />
</form>
```



