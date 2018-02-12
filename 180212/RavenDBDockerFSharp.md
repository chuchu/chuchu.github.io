# RavenDB, Docker and F#

Here is a little F# application which connects to a RaveDB database, adds an entry and reads them. I used this application to play with RavenDB running inside a Docker container.

```fsharp
open Raven.Client.Documents

type Customer = {
    FirstName: string
    LastName: string
}

[<EntryPoint>]
let main argv =
    printfn "Hello RaveDB!"

    use store = new DocumentStore ()
    store.Urls <-  [|"http://10.0.75.1:8080"|]
    store.Database <- "RavenDB1"
    use store = store.Initialize ()

    use session = store.OpenSession ()
    let customer1 = { FirstName = "Guy"; LastName = "Montag" }
    session.Store customer1
    session.SaveChanges ()

    let customers = query {
        for customer in session.Query<Customer>() do
        select customer
    }

    customers |> Seq.iter (printf "%A")

    printfn "Done!"
    0
```

From the code you can see that the database runs on 10.0.75.1 which is the IP address of my Docker bridge.

To execute a RavenDB Docker container you need to download the image first. You can do this by executing the command:

```batch
docker pull ravendb/ravendb
```

This will download the latest ubuntu based version. To run the image its best to use the run-ubuntu1604.ps1 powershell script which is provided by the RavenDB project.

```batch
ubuntu1604.ps1 -AuthenticationDisabled -PublicServerUrl "http://10.0.75.1:8080"
```

Disabling authentication is good for testing purposes. So there is no need to deal with certificates. Why it is necessary to provide the PublicServerUrl is something what I don’t really understand completely. But this article explains it a little bit.

https://ayende.com/blog/178819/bug-stories-how-do-i-call-myself

Now you can open your browser, go to http://10.0.75.1:8080 and create the database RavenDB1. When the database is up and running the application can be executed.

```batch
dotnet run
```

When everything works as expected the F# application can be deployed in a container too. Before the image can be created you need to make a release build.

```batch
dotnet publish -c release -o app
```

The docker file is available in the repository. So you just need to type

```batch
docker build -t hello-ravendb .
```

Have a look for the line

```
Successfully built 388344c214f3
```

Your ID is surely different, but you need it to run your image.

```batch
docker run 388344c214f3
```

You should see the same output as when the application was executed directly.

<div id="disqus_thread"></div>
<script>
    var disqus_config = function () {
        this.page.url = "https://chuchu.github.io/180212/RavenDBDockerFSharp.html";
        this.page.identifier = "180212_RavenDBDockerFSharp";
    };
    (function() {
        var d = document, s = d.createElement('script');
        s.src = 'https://chuchuchuchu.disqus.com/embed.js';
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>If you want to comment, please enable JavaScript.</noscript>