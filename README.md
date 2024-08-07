# Blazor 8 Root Path Variable Defect

This sample project is to reproduce the defect in Blazor 8 Interactive Server: 

***HelloWorldClass.js:1 Uncaught SyntaxError: Unexpected token '<'***

TLDR; Blazor 8 cannot handle variables in a root path and begins serving static files (CSS and JavaScript) as garbage.  

This worked in Blazor 7 but not in Blazor 8:

`@page "/{mylocation}/{mypageName}"`

You have to put this in Blazor 8:

`@page "/{mylocation}/{mypageName:nonfile}"`

Defect logged to Microsoft:

    https://github.com/dotnet/aspnetcore/issues/57192



Steps to reproduce the error:
1.  In Visual Studio 2022, create a Blazor 8 Web App.
2.  Choosing Interactive Render Mode of Server.  
3.  Choose Interactivity location of Per page/component.
4.  Include sample pages
5.  After the solution is created, open App.razor
6.  Change HeadOutlet to this:

    ```html
    <HeadOutlet @rendermode="RenderMode.InteractiveServer" />
    ```
7.  Change Routes to this:

    ```html 
    <Routes @rendermode="RenderMode.InteractiveServer" />
    ```

8.  Change Home.razor to this:

```html
@page "/"
@page "/{mylocation}/{mypageName:nonfile}"

<PageTitle>Home</PageTitle>

<h1>Hello, world!</h1>

Welcome to your new app.

@((MarkupString)BodyContent)

@code {
    [Parameter] public string? mylocation { get; set; }
    [Parameter] public string? mypageName { get; set; }
    private string BodyContent = "";

    protected override void OnInitialized()
    {
        if (mylocation == null)
        {
            mylocation = "National";
        }

        if (mypageName == null)
        {
            mypageName = "Home";
        }

        BodyContent = $"<p>Location: {mylocation}</p><p>Page Name: {mypageName}</p>";
    }
}
```
9. In the wwwroot folder, create a scripts folder.
10.  Create a JavaScript file called HelloWorldClass.js in the scripts folder with this content:
```javascript
window.HelloWorldClass = {
    HelloWorld: function () {
        alert('Hello World');
    }
}
```
11.  Run the solution
12.  Do a Ctrl-F5 in the Browser to do a hard refresh


