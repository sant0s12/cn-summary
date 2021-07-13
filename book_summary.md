---
geometry: top=1cm, bottom=1cm, left=1cm, right=1cm
header-includes: |
    \pagenumbering{gobble}
    \usepackage{framed}
    \usepackage{xcolor}
    \let\oldquote=\quote
    \let\endoldquote=\endquote
    \colorlet{shadecolor}{orange!15}
    \renewenvironment{quote}{\begin{shaded*}}{\end{shaded*}}
    \twocolumn
...

# 1. Computer Networks and the Internet

## 1.1 What is the Internet?
The internet connects many devices (called hosts and
clients). It uses different media at different transmission rates (speed). The
information is encapsulated into packets and sent through the network.

*Routers* and *link-layer switches* are tasked with forwarding the packets
towards the correct destination.

*End systems* connect to the internet through an *ISP* (Internet Service
Provider), which are also interconnected between each other (tiered system).

Protocols are the what enables the communication between the different devices
and softwares on the network. The Internet Standards exist so that everyone can
agree on what protocols to use and how they operate.

> A protocol defines the format and the order of messages exchanged between two
> or more communicating entities, as well as the actions taken on the
> transmission and/or receipt of a message or other event.

## 1.2 The Network Edge

The Access Network
: The network that physically connects and end system to the
first router (aka. "edge router")

![](images/access_network.png)

DSL (Digital Subscriber Line) is still somewhat popular. DSL is typically
provided by the phone company because it uses the already existing telephone
wires. Due to the low bandwidth, downstream is usually higher than upstream. The
distance between the home and the *Central Office* (CO) also plays a role in
maximum speed.

Another popular technology is cable. This is provided by the cable television's
company existing infrastructure.

![](images/fiber_coaxial.png)

Fiber to the home consists of bringing fiber access from the CO directly to
peoples' homes.

The Physical Medium
: Physical device that actually transmits the signals (e.g.
twisted-pair copper wire, fiber-optic, coaxial, etc.). Two categories:
**guided** and **unguided**. With guided media, the waves travel along a solid
medium while with unguided media they travel in the atmosphere.

## 1.3 The Network Core
End systems exchange messages withe each other. These
messages are broken into smaller chunks known as *packets*, which are then
transmitted through links and packets switches at the full transmission rate of
the link. That is, if the packet size is $L$ bits, then the time to transmit the
packet over a link with transmission rate $R$ bits/second is $\frac{L}{R}$
seconds.

*Store and forward* transmission means that the switch has to receive the entire
packet before it can be forwarded. For the general case of transmitting a packet
over a path with $N$ links (each of rate $R$) is $\frac{NL}{R}$.

![](images/store_and_forward.png)

Packets might also suffer from *queuing delay*, which is the time a packet has
to wait until all packets queued before are transmitted and the current packet
can be sent. If the queue is full, then packet loss can occur as there is no
space to save the packet.

In order to know where to send packets, each router has a forwarding table. This
table maps IP ranges to outbound links. They are set up using routing protocols.

### Circuit switching
Paths connecting two systems are reserved for a period of
time (e.g. traditional telephone networks). This connection is called
a *circuit*. The transmission rate between sender and reviver is guaranteed,
which is completely different to *packet switching*, where the network makes
a "best effort" to deliver the packet.

There are ways to multiplex a circuit switched network: Frequency-division
Multiplexing (FDM) or Time-division Multiplexing (TDM). The first one divides
the frequency spectrum of the link among different connections while the second
one divides the time into slot which are available in each frame and dedicates
each slot to one connection.

![](images/circuit_multiplex.png)

**Circuit switching:**\ Pros:

- Guaranteed rate
- Predictable

Cons:

- Silent periods
- Complicated

**Packet switching:**\ Pros:

- Better sharing of transmission capacity
- Simpler
- Less costly

Cons:

- Unpredictable queuing delays
- No guaranteed speed

Trend today towards packet switching!

## 1.4 Delay, Loss, and Throughput in Packet-Switched Networks

Processing delay
: Time to examine the packet's header and determine where to
direct the packet. Can also include checking for bit-level errors. In the order
of microseconds.

Queuing delay
: Delay the packet experiences while being in a queue waiting to
be transmitted onto the link. Queuing delays depend on how many packets are
arriving at the queue. In the order of microseconds to milliseconds.

Transmission delay
: The transmission delay for a packet of $L$ bits on a link
with rate $R$ is $\frac{L}{R}$.

Propagation delay
: The time required to propagate from the beginning of a link
to the end. It depends on the physical medium. It is equal the distance divided
by the speed of light in that medium. 

Queuing delay is the most interesting, it can change from packet to packet. The
traffic intensity serves to gain some insight into the queuing delay. Let $a$ be
the average rate at which packets arrive in the queue. Then the traffic
intensity $TI$ is the ratio $\frac{L \cdot a}{R}$. For $TI >$ 1 then the queue
size tends to increase without bound (assuming infinite size). For $TI \leq 1$,
then the nature of the traffic matters (e.g bursty, periodic, etc.).
Realistically: Arrival is random.

![](images/queing_delay.png)

Without the assumption that the queue has infinite size, packets that do not fit
will be dropped causing *packet loss*. To the endpoints this just looks like the
packed disappeared.

End to end delay
: Total packet delay from source to destination ($d_{\text{end-end}}
= N(d_{\text{proc} + d_{\text{trans}} + d_{\text{prop}}})$).

Instantaneous Throughput
: Rate (in bits/sec) at which the receiving host is receiving a message.

Average Throughput
: Throughput averaged over e.g. a file transfer.

The throughput between two communicating systems is the rate of the *bottleneck
link* (the rate of the link with the minimum bandwidth).

![](images/bottleneck_link.png)

The access network is typically the bottleneck in today's internet.

## 1.5 Protocol Layers and Their Service Models

![](images/protocol_stack.png)

The Internet makes use of a layered architecture. Each protocol belongs to one
of the layers. They can be implemented in software, hardware or both. This
systems makes it easier to discuss specific parts of the system as well as
updating specific components.

Application Layer
: Network applications and their application-level protocols (e.g. HTTP, SMTP,
FTP, ...)

Transport Layer
: Transports application layer messages between application endpoints. TCP and
UDP.

Network Layer
: Transports network layer packets (*datagrams*) from one host to another. This
is the IP (*Internet Protocol*) protocol. All devices that have a network layer
must support the IP protocol.

Link Layer
: Delivers datagrams between link layer nodes (e.g. WIFI, Ethernet, etc.).

Physical Layer
: Transport the individual bits between nodes.

The concept of *encapsulation* is very important. As a message is from the
application layer to lower layers, more information is added. This information
is used in the respective layers and then stripped away again when the message
is carried to higher layers. Because of this, not all network devices implement
all the layers, just the ones that are required to perform the required task. 

![](images/layers_switches.png)

# 2. Application Layer

## 2.2 The Web and HTTP

HTTP is the Web's application layer protocol with a client side and a server
side. They communicate via HTTP requests and responses. TCP is the underlying
protocol. Importantly, HTTP is stateless.

Persistent Connection
: Perform a series of HTTP requests/responses using the same TCP connection
every time

Non-persistent Connection
: Use a new TCP connection every time a client wants to make a new HTTP request.

Round-trip Time (RTT)
: Time for a small packet to travel from client to server and back.

Using non-persistent TCP connections for web requests forces the client and
server to create a new connection for each request, which costs 2RTT each time.
This is why the default is to keep the connection alive.

HTTP requests and responses are written in ASCII so they are human-readable.
The HTTP methods are one of GET, POST, HEAD, PUT or DELETE. Most of HTTP
requests use either the GET or POST methods. With the POST method, the user can
send specific data on the request (like form information). The HEAD method is
similar to the GET method but it only responds with the HEAD.

Cookies
: Allow sites to keep track of users. 4 components:\
    1. Cookie header line in HTTP response\
    2. Cookie header line in HTTP request\
    3. Cookie file kept on the user's machine\
    4. Database on the web server

![](images/cookies.png)

Web Cache
: Proxy server used to keep copies of recently requested objects and respond to
HTTP requests on behalf of the main HTTP server. If the cache server does not
have the requested file/site, it performs a HTTP request to the origin server
and serves it back to the client. Now the cache will have a copy of this served
file and it will be available for future clients (until it is removed/replaced).

**Reasons for deploying a web cache:**

- Reduce response time (especially if client has fast connection to the 
    cache and cache has local copy)
- Reduce institution's traffic (if local cache)
- Reduce total Internet traffic 

Caching also introdues the problem of not having the most recent version stored
at the web cache server. Conditional GET solves this problem by allowing the
cache server to perform a get request to the original server with a specified
modification date. If the content has been modified since, the server will reply
with the new file, otherwise the body will be empty and the cache server can
just serve the cached file.

![](images/institutional_cache.png)

## 2.4 DNS


