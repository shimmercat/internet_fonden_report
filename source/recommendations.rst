
Recommendations
===============

Most of the recommendations below has been made before in the IETF mailing list
discussion list.
See for example: https://www.ietf.org/mail-archive/web/httpbisa/current/msg26427.html .


Introduce cache digests in browser
----------------------------------

The current draft is a step in the right direction, and we very much would like it
seen to completion.


Make it possible to opt-out of cache digests, or make them opt-in
-----------------------------------------------------------------

In some scenarios cache digests could be problematic.
For example, for domains whose contents evolve rapidly and where
the number of contents is high.
The effect of that behavior is that cache digests tend to be big
and their effectivity low (due to false positives).
In those scenarios, switching cache digests off would help.

One very desirable way to make this work is by having the server
to add a settings in the SETTINGS frame of HTTP/2, or a special HTTP
header that says what's the maximum size that a cache digest
can reach.


Although not strictly needed, scoping would be highly desirable
---------------------------------------------------------------

Scoping is about segregating the digest in compartiments.
Kazuho for example has proposed to use URL paths to that end.

As we saw in the "Data" section, the typical size today of a cache
for a given orgin is not so big that size alone be a problem.

Nevertheless, it is far too easy to find cases where it will be needed.
For example, if a single domain hosts two different applications,
it doesn't make sense to include assets of one in the cache digest
of the other.
Equally if there are "priority tiers" in a single application.
For example, CSS styles are more important than images from the
perspective of what is needed to render the page first, but a
large number of images in the cache digest may make the digest
for the entire site too big or prone to false positives.
In that situation, segregating the two caches would be a desirable.

Without scoping, website operators will still require domain
sharding, a technique that uses different origins as scopes.
