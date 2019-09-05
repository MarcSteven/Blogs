# Scale your rails application

- [@MarcSteven](https://twitter.com/marcstevencoder)
---


* 1，Choose a multi-process web server with slow client protection and smart routing/pooling. Currently, your only choices are Puma (in clustered mode), Unicorn with an nginx frontend, or Phusion Passenger 5.
* 2，Scaling dynos increases throughput, not application speed. If your app is slow, scaling should not be your first reflex.
* 3，Host/dyno counts must obey Little’s Law.
* 4，Queue times are important - if queue times are low (<10ms), scaling hosts is pointless.
Realize you have three levers - increasing application instances, decreasing response times, and decreasing response time variability. A scalable application that requires fewer instances will have fast response times and low response time variability.

