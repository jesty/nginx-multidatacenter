# Nginx passive/active in a multi-datacenter/geo-replicated scenario.

The demo provides an Nginx setup to serve a passive/active service in a multi-datacenter/geo-replicated scenario.

Using this simple setup we can route our clients to the closest datacenter, depending on their location, and if a data center, or a
container, will not be more available, we can use a backup server on another datacenter/geographic location.
Obviously the responsibility to replicate data will be on charge of other systems.


In this demo, we will start an Nginx load balancer that redirects the client that performs request without header to a datacenter
and the client that uses the header "X-SERVER-SELECT" with value "two" to another datacenter.

When a container on a datacenter, on entire datacenter, will not be more available, the load balancer will redirect the service
invocation to the backup node.


To run the demo execute:

	$ docker-compose up

and then visit http://localhost:9090 the string "Test ONE" will appear to you.

Then try to contact using the header "X-SERVER-SELECT" with value "two":

	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be: "Test TWO".

Now try to stop the main server that refers to the backend "ONE"

	$ docker stop backendTwo

And re-execute the curl above:
	
	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be "Test TWO Backup". Now re-start the container:

	$ docker start backendTWO

and retry the curl:

	$ curl --header "X-Server-Select:two" http://localhost:9090

The result will be again: "Test TWO".
