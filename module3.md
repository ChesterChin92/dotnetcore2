# Module 3: Introduction to Routing & MVC 

## Introduction to MVC 

**[What is the MVC pattern?](https://docs.asp.net/en/latest/mvc/overview.html)**

![](https://raw.githubusercontent.com/LadyNaggaga/ASP.NETCoreMVA/master/Images/MVCPattern.png)

*The illustration above is a simple explanation of the  MVC pattern.*

When a you the visits a website the following things happen 
- You request a to view a page by entering a URL. 
- The **Controller** recieves the page request. 
- **Controller** sends the request to the **Model** to retrieve all the requested data.
- The **Model** stores and packages the data to be presented to you in the **View**

<br><br>

## Create a new MVC Web Application 
- File New Project -> .NET Core -> ASP.NET Core -> Web Application

![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/create-web-app.gif)
- Add a "Controllers" folder to your application
- Create a new class called "HomeController" in the new folder and add the following code:

```c#
using Microsoft.AspNetCore.Mvc;

public class HomeController
{
  [HttpGet("/")]
  public string Index() => "Hello from MVC!";
}
```
![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/change-controller-code.gif)

- Run the site and verify the message is returned from your MVC controller
- If you have time, try the following:
  - Change the controller to render a view instead of returning a string directly
  - Play with the `[HttpGet("/")]` attribute to change the route the action method will match

Appendix : Routing Internals


##
 Using Routing Middleware

- Open the Startup.cs file
- Add a routing services to ConfigureServices method in the Startup.cs
```C#
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddRouting();
        }
```

- In the `Configure` method, create a `RouteBuilder` with a handler for the root of the site and add it to the middleware pipeline:
  
```C#
  using Microsoft.AspNetCore.Routing;
  ...
  public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
  {
      loggerFactory.AddConsole();

      if (env.IsDevelopment())
      {
          app.UseDeveloperExceptionPage();
      }

      var routeBuilder = new RouteBuilder(app);

      routeBuilder.MapGet("", context => context.Response.WriteAsync("Hello from Routing!"));

      app.UseRouter(routeBuilder.Build());
  }
  ```
![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/change-startup-cs.gif)

> **Note To Chester** Unknown Reason Unable to overwrite route mapping
  - Run the site and verify your middleware is hit via routing (Ctrl+F5)
  - Add another route that matches a sub-path:

  ``` c#
  routeBuilder.MapGet("sub", context => context.Response.WriteAsync("Hello from sub!"));
  ```
  ![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/sub.gif)

### Capture and Use Data 
- Add another route that captures the name of an item from the URL, e.g. "item/{itemName}", and displays it in the response:
  
  ``` c#
  routeBuilder.MapGet("item/{itemName}", context => context.Response.WriteAsync($"Item: {context.GetRouteValue("itemName")}");
  ```
  ![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/item-name.gif)

-  Run the site and verify that your new route works. Browsing to "/item/monkey" should display the message "Item: monkey".
- Modify the route to include a route constraint on the captured segmet, enforcing it to be a number:
  
  ``` c#
  routeBuilder.MapGet("item/{id:int}", context => context.Response.WriteAsync($"Item ID: {context.GetRouteValue("id")}"));
  ```
   ![](https://raw.githubusercontent.com/AlexTang0620/aspdotnet_gif/master/module3-gif/item-id.gif)
-  Run the site again and see that the route is only matched when the captured segment is a valid number. Browsing to "/item/5" will work, but browsing to "/item/monkey" will now show a missing page (HTTP 404) error.
-  Modify the router to include both versions of the route above (with and without the route constraint)
- Experiment with changing the order the routes are added and observe what affect that has on which route is matched for a given URL


