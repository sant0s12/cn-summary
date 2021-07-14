---
toc: 1
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

Hostname
: Mnemonic, human readable identifier for a host.

While hostnames are useful for humans, IP addresses are what identify systems
within the Internet. DNS takes care of translating these nice human readable
names into useful IP addresses.

DNS also provides other services:

- Host aliasing
    : Alias hosts with complicated canonical hostnames to one or more other
    user-friendly names.
- Mail server aliasing
    : MX records so that mail and web servers can have the same name while
    pointing to different canonical hostnames.
- Load distribution
    : Sites with lots of traffic are replicated across different servers. All
    those are mapped to the same hostname but the DNS server lists them in
    a different order every time to balance the load.

Centralized single DNS databases do not scale, that is why DNS is a distributed
system composed of *root*, *top-level domain* and *authoritative
DNS servers*.

Root DNS Servers
: Provide IP addresses of the TLD servers.

Top-level Domain Servers
: (e.g. com, org, net, edu and all the country top-level domains), provide the
IP addresses of the authoritative DNS servers.

Authoritative DNS Servers
: Stores mappings between public hostnames and IP addresses for publicly
accessible hosts.

Local DNS Server
: Not strictly part of the hierarchy. This server forwards the DNS query made by
the client to the hierarchy.

![](images/dns_query.png)

Recursive Query
: Asking the DNS server to perform the query on your behalf.

Iterative Query
: Query the DNS server. It will respond with the IP address of the next DNS
server but will not perform the request for you.

DNS Caching
: Any DNS server that receives a DNS reply can cache the mapping locally so that
a future DNS request is not necessary. It can provide the mapping even if it is
not the authoritative DNS server. This is discarded after some time.

Registrar
: Commercial entity that verifies the uniqueness of the domain name and enters the
domain name into the DNS database.

## 2.6 Video Streaming and Content Distribution Networks

Video streaming accounts for the majority of traffic in residential ISPs.

Prerecorded videos placed on servers, they can be accessed by users *on demand*.
Videos are just arrays of pixels displayed at usually a constant amounts of
frames per second (*FPS*). They can be compressed. The average throughput of the
network must be on average higher than the video's bitrate in order to stream
the video without buffering.

Dynamic Adaptive Streaming over HTTP (DASH)
: Solves the problem of every client receiving the same encoding of the video
independently of network conditions or bandwidth. This is done by splitting the
video into different chunks with different bitrates which the client can chose
depending of the available bandwidth. The bandwidth is determined using an
algorithm while downloading other chunks.

Distributing video content all around the world can be challenging if a company
were to only have one central data center. This is (amongst other reasons because
they would not be in control of bottleneck links and would have to pay the ISPs
to use large amounts of bandwidth (maybe even sending the same content many
times).

Content Distribution Networks
: Distribution of servers across the world with copies of the content which is
to be delivered to the user. When a user connects to the service, the server
which provides the best experience will be used. CDNs try to solve the
challenges mentioned above.

**Two server placement philosophies:**

- Enter Deep
    : Deploy server clusters in access ISPs all over the world. Goal: get closer
    to end users. Problem: Maintaining clusters can be hard.

- Bring Home
    : Small amount of clusters at Internet Exchange Points (IXPs). Goal: lower
    maintenance. Problem: possible higher delays.

DNS is used to redirect traffic from sites to the CDNs by replying to the DNS
request with the a hostname on the CDN domain instead of an IP address. That
way, the DNS request is "handed over".

Cluster selection might be done based on the location of the IP address of the
local DNS server (LDNS) when the DNS request is made. This can be inaccurate
because geographical closeness does not always mean better connection. For
better estimates, real time measurements are needed.

# 3. Transport Layer

## 3.1 Introduction to Transport-Layer Services

They provide *logical communication* between applications running on different
hosts, a layer of abstraction that creates the illusion of a direct connection
and ignores the complexity of the underlying layers.

The transport layer is not implemented by network devices but end to end
systems. The current Internet has two protocols, TCP and UDP. They are
responsible of delivering application layer messages to the network layer
(possibly by breaking them apart into smaller chunks). However, they do not
affect how the packets are sent along the network, that is the responsibility
of the network layer. Different protocols may offer different service models but
they are constrained by the capabilities of the network later protocols.

User Datagram Protocol (UDP)
: Unreliable, connectionless

Transmission Control Protocol (TCP)
: Reliable, connection-oriented

UDP and TCP extend the IP protocol from communication between systems to
communication between processes. This is done by *transport-layer multiplexing*
and *demultiplexing*. They also provide some integrity checking.

## 3.2 Multiplexing and Demultiplexing

Transport layer receives segment form the network layer and is supposed to
deliver the message to the correct process running on the host. Because
applications can have more than one connection open to the network (so called
*sockets*), the transport layer does not deliver the message directly but
instead does so via one of the open sockets.

Demultiplexing
: Delivering data from a transport-layer segment to the correct socket.

Multiplexing
: Gathering data chunks from different sockets and encapsulating each chunk in
transport-layer segments to be passed to the network layer.

![](images/multiplexing_demultiplexing.png)

For multiplexing/demultiplexing to work, ports must have unique identifiers and
the transport-layer headers must indicate the target socket.

There are some "well-known" port numbers which are used for specific
protocols/services (e.g. 80 for HTTP or 21 for FTP).

### UDP Multiplexing/Demultiplexing

Application Data → Transport-layer segment including source port and destination
port → Network Layer → IP Datagram → Receiving Host → Receiving socket on
destination port

A UDP socket is fully identified by the destination IP address and destination
port number. The source port number is not taken into account. Instead, it is
used for the receiving host to communicate back with the sending host.

### TCP Multiplexing/Demultiplexing

TCP sockets are identified by a source IP and port number, and a destination IP
and port number. This is why a packet from host A to port P and a packet from
another host B to the same port P will not map to the same socket on the
receiving system.

This is useful for web servers, as the HTTP request have destination port 80 and
would not be able to be correctly demultiplexed if TCP sockets were only
identified like UDP sockets. Web servers will then spawn new threads/processes
with respectively new sockets for each connection. These will then be
reused/closed depending on if the connection is persistent or not.
