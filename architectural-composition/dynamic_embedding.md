# Dynamic Embedding

In the previous subsections we showed how to statically embed services. A statically embedded service is shared among all sessions created by the embedder, enabling any of them to access shared \(persistent\) data.

Dynamic embedding makes possible to associate a unique embedded instance \(session\) to the embedder, thus allowing only that specific instance to invoke the operations of the embedded service.

Let us consider a specification of a service that allows clients to start a timed-counter. The counter sends back to its invoker the value of an incremental count every second.

First, we implement the specification as an embeddeder with a `concurrent` behaviour, statically embedding a counter service. A new instance of the embedder is created each time a new client invokes the `startNewCounter` operation, but still the embedded service `CounterService` is instanced only once and it is shared among all instances of the embedder.

```text
/*
    part of deployment omitted
*/

outputPort CounterService{
    Interfaces: CounterInterface
}

embedded {
    Jolie: "CounterService.ol" in CounterService
}

execution{ concurrent }

main
{
    startNewCounter( location );
    CounterClient.location = location;
    println@Console( "New counter session started" )();
    start@CounterService();
    while( true ){
        inc@CounterService()( counterState );
        receiveCount@CounterClient( counterState );
        sleep@Time( 1000 )()
    }
}
```

Such an implementation is wrong because it diverges from the given specification.

`CounterService` is a singleton, its operations are accessed by any instance of the embedder, thus each instance of the embedder returns a "global" counting, instead of a single-instanced one.

We can achieve the right implementation by dynamically embedding the service.

```text
include "runtime.iol"

/*
    part of deployment omitted
*/

outputPort CounterService{
    Interfaces: CounterInterface
}

execution{ concurrent }

main
{
    startNewCounter( location );
    CounterClient.location = location;
    println@Console( "New counter session started" )();

    embedInfo.type = "Jolie";
    embedInfo.filepath = "CounterService.ol";
    loadEmbeddedService@Runtime( embedInfo )( CounterService.location );

    start@CounterService();
    while( true ){
        inc@CounterService()( counterState );
        receiveCount@CounterClient( counterState );
        sleep@Time( 1000 )()
    }
}
```

In the example above we include the `runtime.iol` library \(part of Jolie's standard library \) used for realizing the dynamic embedding. At Lines 15-17 we dynamically embed the service `CounterService` which creates a new instance of the embedded service each time a new instance of the embedder is started.

The comprehensive code examples of both the static \(wrong\) and dynamic embedding can be downloaded here:

[Dynamic Embedding Code Example](https://github.com/jolie/docs/blob/master/files/architectural-composition/code/dynamic_embedding_code.zip)

