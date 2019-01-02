## Embedding a Jolie service

Let us consider the *twice* service example given in [Behaviour and Deployment](/documentation/getting_started/behavior_and_deployment.html) sub-section.

First, we add the following input port to allow local communications:

<pre><code class="language-jolie code">include "twiceInterface.iol"
 
inputPort LocalIn {
	Location: "local"
	Interfaces: TwiceInterface
}
 
main
{
    twice( number )( result ) {
        result = number * 2
    }
}
</code></pre>

Afterwards, we can write a modified version of the client program which embeds the twice service and calls it using an output port bound by embedding. We assume that the embedded service is stored in `twice_service.ol`.

<pre><code class="language-jolie code">include "twiceInterface.iol"
include "console.iol"

outputPort TwiceService {
	Interfaces: TwiceInterface
}

embedded {
	Jolie: "twice_service.ol" in TwiceService
}

main
{
	twice@TwiceService( 5 )( response );
	println@Console( response )()
}
</code></pre>

When embedding a Jolie service, the path URL must point to a file containing a Jolie program (provided as source code or in binary form).