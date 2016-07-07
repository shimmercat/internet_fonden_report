Preliminaries
=============

To better understand the context in which the HTTP/2 Push technologies
and cache digests can be employed, we have added some contextual data in
this section.

How much web traffic is out there anyway?
-----------------------------------------

At the time of this writing, the number of websites is stable around
``1 billion <http://www.internetlivestats.com/total-number-of-websites/>``\ \_.
However, people don't visit those sites with the same frequency. In
fact, most traffic goes to just a few sites.

Here is a table with a few websites that we obtained from
``SimilarWeb <https://www.similarweb.com/>``\ \_:


==================== ======== ======================
Site                  Rank    Total visits per month
==================== ======== ======================
facebook.com                3                  28e9
dictionary.com            688                  80e6
python.org               1788                  22e6
gcc.gnu.org              4743                   2e6
netcraft.com            52185                 655e3
sphinx.org             117945                 256e3
internetfonden.se     4056955                   3e3
==================== ======== ======================


SimilarWeb provides only estimations, but we compared their estimations
with some websites to which we have access to the Analytics data and we
could see that the orders of magnitude were correct in all instances.

This leads to a simple conclusion: most websites out there don't need
more than one CPU to handle all the traffic that they receive. Or put in
less controversial terms, for each website out there that needs to load
balance its traffic between several CPUs, there are a few thousand
websites that can work fine with a single CPU.

Other than traffic, there may be other legitimate reasons to distribute the
servers of a website geographically. One of them is redundancy. But the
reason that most often network administrators cite is the degradation on
speed due to network latency.

For most websites, latency is the primary performance killer
------------------------------------------------------------

Latency is the time required by a web request to go from browser to
server and back. Since browsers often need to request assets in
sequence, the degradation on performance implied by latency tends to
amplify itself.

The fact of latency being the primary performance killer is widely known,
for two reputable sources, see:

-  `A blog
   post <https://www.igvita.com/2012/07/19/latency-the-new-web-performance-bottleneck/>`__
   from Ilya Grigorik, one of Google's performance engineers.
-  `A blog
   post <http://rescomp.stanford.edu/~cheshire/rants/Latency.html>`__
   from Stuart Cheshire, as far back as 1996

HTTP/2 Push and Cache digests
-----------------------------

HTTP/2 was approved as an Internet Protocol in 2015 in `RFC
7540 <https://tools.ietf.org/html/rfc7540>`__. HTTP/2 is at its core a
frames protocol. Frames are part of streams, and streams are identified
by a number. The stream number is present in each frame as a binary
field. Streams -- and their numbers -- allow to match requests and
responses.

As by RFC 7540, *"HTTP/2 enables a more efficient use of network
resources and a reduced perception of latency*". One of the techniques
that help reduce the effects of latency is HTTP/2 Push. It does so by
allowing a web server to send resources to a browser before the browser
gets to request them.

In order to save network resources when doing HTTP/2 Push, a web server
should know which assets the browser already has in its cache. Here is
where cache digests enter. Cache digests were first implemented by
Kazuho Oku in his H2O server, and then published by him and Mark
Nottingham as an `Internet
draft <https://tools.ietf.org/html/draft-kazuho-h2-cache-digest-00>`__.


How cache digests work
----------------------

The cache digest mechanism is simple: when a browser starts fetching
a web page, it sends the server a very abridged report of all the files from
the site that it already has.
This report is what is called a cache digest.

The server uses the digest to select what to push.
That is, if the web page needs a file called `styles.css`, that file is only
sent when it is not present in the digest.

The two paragraphs above describe the very high level view.
The rest of this report contains some data that hopefully will help
inform decisions concerning this draft.
