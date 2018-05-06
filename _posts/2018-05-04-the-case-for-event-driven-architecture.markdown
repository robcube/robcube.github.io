**The Case for Event-driven Architecture**

Robert Koch, Enterprise Architect

25 April 2018

Whitepaper

Business is basically a bunch of events that we strive to organize from
chaos. We would need to translate all those events into something we can
program. Some of you might call it domain driven design, however we are
going to take it a step further and discuss integration of the various
domains. Event-driven architecture is different than what we commonly
use here doing batch-oriented workflows or command-and-control systems.
Event-driven architecture is a concept that has been around for quite a
while; however, this integration pattern is ramping up as the
predominant pattern for enterprise integration patterns. Even IBM is
using event messaging streams to supplement their product offerings. It
is now being utilized by more and more businesses since the technology
has finally been caught up. With batch-oriented architecture, we\'re
spending our time waiting for the data, and then when the batch job time
comes, you hope the data is present. If the data is not there at a
predefined time, then we resort to doing manual activities or a process
throwing errors until the data finally arrives.

Event-driven architecture is designed to move at the rate we move our
data. If we move our data around rapidly, it\'ll be able to keep up, and
if ~~supposedly~~ we move the data deliberately, it\'ll match that same
pace. Another added benefit is the flexibility it offers when we
integrate our system hodge podge. For example, if we need to add a
component or a service, we just plug it into the bus and it\'ll just
work with no source mapping or configuration. The onus in consuming the
data the right way would be on the consuming systems after all the
consumers are the domain experts.

If you\'re wondering what is so special about this architecture, here is
a series of diagrams that hopefully can show the big picture of the
benefits of event-driven architecture.

Let's look at the below diagram illustrating a legacy batch-driven
architecture. Naturally as with any old systems, at no blame to anyone,
it was built with the knowledge we had at the time, while at the same
time the event-driven technology wasn\'t readily available and was
probably cost-prohibitive. I try not to critique older systems as there
is always a reason behind it: cost, time, skills, circumstances, etc.
Rebuilds are usually enjoyable with newer concepts and approaches in
your back pocket.

Pay special attention to all the clock icons in the workflow diagram.

![batch processing looking normal]({{ "/assets/eda-1.jpg" | absolute_url }})

This shows that the process is batch-driven (denoted by a clock) and
when any of the subsequent processes run, we hope that nothing can go
wrong and upend the rest of the workflow. Here is an example of a
situation where the data might not be present causing issues down the
road, requiring immediate manual intervention.

What can we do in the meantime is to solve this issue where there is no
data for the day as thus breaking the rest of the system. We could make
a workaround where we can proxy (duplicate yesterday\'s data) as a
temporary measure until the actual data comes in. If we don\'t have
today\'s data, the models would be impacted since it wouldn\'t be able
to forecast accurately. It would also impact the aggregation service
because there\'s no new data to categorize!

So since we have a system in place to proxy the data, let\'s see what
happens here \--

The customer derives no extra value from the data being proxied. Again,
the customers demand timely reports and it is our job to deliver what
they want even though they may not gain the extra value. The problem is
that we CAN provide them the value, but how? We're stuck in a rock and a
hard place situation where we\'d have to satisfy customers and at the
same time deliver "good enough" data.

Now let\'s pause here from a workable system, and take a look at a
diagram of an event-driven architecture using the same systems we are
using above \--

Note the diagram has been changed from a sequential left to right to a
spoke-like system. Some of you might wonder what the numbers ( ①②③④ )
means. This means ordered steps in the workflow where the data may need
to do normalization and some aggregations, while other services will
require some time to process the data and generate datasets for the
workflow down the pipe. You could even perceive a spoke system where
each of the services would process the data all at the same time!

The customer would have the advantage of near-time data available to
them as soon as we collect them.

We can create new products for customers easily using the event message
stream. Customers would be able to tap into the intelligence we have in
our products to supplement their own learning systems. Whenever a
customer asks for a product that we haven\'t created yet, we could
deliver without too much orchestration involving the event message
stream in real-time (ok, ok, near-time for those nit-picking). This is
what it might look like:

In the example above, we plugged in a \'New Service\' (in orange) into
the Event Message Stream and provided the customer with the value of
that additional service. The '?' denotes that this service isn't bound
to a particular step in the workflow. It could be something that goes in
between ① and ② or at the end of the whole stream. The customer walks
away happy knowing we provided them the data as quick as possible. The
new pluggable service could be something like a risk assessment service
or a fraud detection service where we'd throw alerts if the data coming
in seems suspect -- we and the customer alike would definitely benefit
from it.

Wait a min, if the data has been delayed, wouldn't the customer derive
no value? Let's step back a bit, look at the timeline of the whole
workflow. Say, in the legacy system, the data collected and making its
journey through the vast array of systems can take 2+ hours or so. With
event-driven systems, remember, the data can go as fast as you want it
to -- so if the data is late, and it only takes 2 to 5 minutes to travel
in the workflow, it is a likelihood that the tardy data in an
event-driven system will likely be faster than what it was before. Once
updated, the information automatically pushes out to the customer.

I hope this shows you how event-driven architecture can transform your
organization's way of thinking in how we can deliver information to our
customers and partners alike.

References:

*How to set your priorities with Business Architecture*, presentation by
Daniel Lambert
<https://www.slideshare.net/DanielLambert4/how-to-set-your-priorities-with-business-architecture-94473490>

*Serverless Architectures on AWS,* by Peter Sbarski: ISBN 9781617293825

*BPM Task*, blog by Gary Samuelson
<http://garysamuelson.com/blog/?p=1413>

*Business Architecture and BPM Alignment*, workshop by Lloyd Dugan and
Neal McWhorter, Business Architecture Guild

*Kafka Streams in Action*, by Bill Bejeck, No ISBN yet, Manning Early
Access Program

And of course the community with their assorted blogs, tweets, articles
that might have influenced my thinking.
