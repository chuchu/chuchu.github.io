# Avoid subscribing to events in constructors

While adopting a unit test I found a piece of code which looks pretty much like this one:

```C#
public Subscriber(Printer printer, string message)
{
    printer.Print += OnPrint;

    if (message == null)
    {
        throw new ArgumentNullException("message");
    }

    myMessage = message;
}
```

This is the constructor of a class called Subscriber. In the first statement it subscribes an external event. Afterwards it checks a message parameter and might throw an exception. The following question comes to my mind. What happens to the event when the exception is thrown? Is there some zombie object assigned to the Print multicast delegate? Indeed it is! When you have the following code:

```C#
var s = new Subscriber(printer, null);
```

You will get an exception, but the instance was created somehow. The assignment to the s variable did not happen because of the exception but it is there. Typically the instance would be deleted with the next garbage collector run, but not when someone keeps a reference. In case of the Subscriber the Printer class keeps a reference with its Print event and so it cannot be garbage collected. What makes it even worse is, that the event handler of the zombie instance is called when the Print event is raised.

So be very careful when subscribing to events in constructors. If anything they should appear as last statement.

<div id="disqus_thread"></div>
<script>
    var disqus_config = function () {
        this.page.url = "https://chuchu.github.io/180212/CTorEvents.html";
        this.page.identifier = "180212_CTorEvents";
    };
    (function() {
        var d = document, s = d.createElement('script');
        s.src = 'https://chuchuchuchu.disqus.com/embed.js';
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>If you want to comment, please enable JavaScript.</noscript>