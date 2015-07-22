title: Implement Permission Control for MVC5 Project
date: 2015-07-21 23:55:00
author: Ji Zhoubo
tags: [C#, MVC]
---

When building backend system for a website, like member management system or accounting system, permission control has always been a must-have feature, so that different staffs or operators can be assigned just enough permissions to perform tasks.

For example, in an accouting system, some operators may only focus on doing money transfer, while some operators only handle reports. There will be information leakage and risks if just grant all permissions for all operators blindly.


# 1. Requirements

![alt text](http://7qn89w.com1.z0.glb.clouddn.com/2015-07-21_013438.jpg)

We start from requirement analysis first.  Assume we have following requirements need to fulfill:
- When an operator log in to system, render menu for him based on his permissions. 
    + for example, if he has permission to view member list, but no permission to view report, then only show member list menu for him

- As long as an operator has permission to view the page, he should has permissions to perform any action available on the page
    + for example, if he has permission to view member list, he can use the update status function provided on the page

- If he try to access pages which he doesn't have permission, should be blocked

- Boss can easily assign permission to an operator, or a group of operators

# 2. General Design

## 2.1 Resource Permission on Controller -> Action
To have control over all pages, we use permission control over all `Actions`.

Why? Because in MVC framework, all requests are passed to `Controller` and processed by `Action`, it makes sense to consider each action as a resource that an unique permission is assigned to.

```SQL
CREATE TABLE [dbo].[Resource]
(
    [ResourceId] INT NOT NULL IDENTITY(1, 1), 
    [ResourceName] VARCHAR(200) NOT NULL,   -- e.g. CreateMember
    [ResourceType] TINYINT NOT NULL, 
    [ParentId] INT NULL, 
    [IsDefault] BIT NULL
    CONSTRAINT [PK_Resource] PRIMARY KEY CLUSTERED
    (
        ResourceId ASC
    )WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY],
)
```

In application, since every action need to have a permission checking, to generalize it, we can put it in `BaseController` (base controller inherits MVC `Controller`):

```csharp
protected override void OnActionExecuting(ActionExecutingContext context)
{
    if (!IsAllowedAction(context))
        {
            context.Result = new HttpUnauthorizedResult();
        }
    
    base.OnActionExecuting(context);
}

private bool IsAllowedAction(ActionExecutingContext context)
{
    var controller = context.RouteData.Values["controller"].ToString();
    var action = context.RouteData.Values["action"].ToString();
    if (AppSession.HasPermission(controller, action))
    {
        return true;
    }
    return false;
}
```

## 2.2 Process flow

The process for display menu by permission is as below

![alt text](http://7qn89w.com1.z0.glb.clouddn.com/2015-07-21_020525.jpg)


# 3. Technical Details

## 3.1 Map Menu with Resource

Each leaf menu node correspond to an `Action`. So we need to have a mapping of Menu with Resource

```sql
CREATE TABLE [dbo].[MenuConfig]
(
    [Key] VARCHAR(200) NOT NULL, 
    [ParentKey] VARCHAR(200) NULL, 
    [ResourceId] INT NULL,
    [IsPageButton] BIT
)

```
After user log in, get all permissions he has, then based on these permissions and the mapping, we can get all menus he can see.

There is a special handling for **IsPageButton**, sometimes in a page, there are buttons which can perform some action (e.g. link to another page, make a POST request).

It is special because we won't display these menus on top menu bar. So this flag is useful when process render menu.

## 3.2 Why Control Resource Permission by Group?

When number of operators grow, it is useful to assign them into different groups, each group perform certain tasks.

To achieve this, during operator creation, will force the creator to map this operator to a group. Then, in the future, this operator's permission will just follow his group's permission.

```sql
CREATE TABLE GroupResourcePermission(
    [GroupId] INT NOT NULL,
    [ResourceId] INT NULL, 
    [IsAllowed] BIT NOT NULL,
)
```


## 3.3 Manage Resource Permission in most straight-forward way!

Below is the update resource permission process:

![alt text](http://7qn89w.com1.z0.glb.clouddn.com/2015-07-22_171605.jpg)


To make it simple to use, we use the same design as the menu bar, just add checkbox for operator to `tick / untick` to `GRANT / DENY` resource permissions.

![alt text](http://7qn89w.com1.z0.glb.clouddn.com/2015-07-22_172337.jpg)

After user submit, backend receive the new menu set, then generate corresponding permissions needed for this new menu set, then compare new permissions with old permissions and find the difference, then update the difference to DB.


# 4. Summary
By reading this blog, you should have a better idea of how to do permission control under MVC framework. Hope it can help in future work!


