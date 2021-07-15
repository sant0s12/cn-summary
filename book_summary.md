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

## 3.3 Connectionless Transport: UDP

UDP implements minor thing on top of IP (multiplexing/demultiplexing and error
checking). The goal is to have an almost direct communication between the
application layer and the network layer. While it is not as robust as TCP, it
provides the following benefits:

- Finer application-level control
    : Direct transport from UDP to network layer, no need to wait on congestion
    control, segment resends, acknowledgment packets, etc.

- No connection establishment
    : UDP can directly begin to send packets instead of first having to setup
    a connection like in TCP.

- No connection state
    : While TCP maintains a connections state, UDP does not. This means less
    overhead and thus better handling of large amounts of connections.

- Small packet overhead
    : TCP has 20 bytes of header overhead while UDP only has 8.

Multimedia applications usually run over UDP, especially if they can tolerate
some packet loss in exchange for better latency because of the lack of
congestion control. This also makes UDP somewhat controversial because it could
be misused and lead to large congestion throughout the network.

If reliability is a must, it can also be implemented on top of UDP (e.g. QUIC).

### Checksum

The checksum field of the UDP header is the 1s compliment (bitwise inverse) of
the sum of all 16-bit words in the segment. The receiver then adds all 16-bit
words (including the checksum) and determines if there is an error in the
packet (if all bits are 16, then there are no errors). There is no mechanism for
error correction, some implementations discard the packet and others hand it
over to the application layer with a warning.

**Example:**

W1: `0110011001100000`\
W2: `0101010101010101`\
W3: `1000111100001100`

W1 + W2: `1011101110110101`\
W1 + W2 + W3: `0100101011000010`\
Checksum: `1011010100111101`

This error checking is important because the IP protocol does not guarantee any
form of error checking on lower layers.

## 3.4 Principles of Reliable Data Transfer

Implement a reliable transport service on top of an unreliable layer (e.g. IP).

### Channel with bit errors

A protocol implemented on this channel will need

- Error detection
    : Detecting bit errors (without correction).

- Receiver feedback
    : ACK for acknowledging a correctly received packet and NAK for the
    contrary.

- Retransmission
    : Retransmit incorrectly received packets.

![](images/automata_1.png)

One possible way of handling corrupted ACK/NAK packets is by resending every
time the sender receives a corrupted ACK/NAK. This introduces duplicate packets.

To solve this one could add sequence numbers (like TCP). Assuming no packets are
lost, a 1-bit sequence number is sufficient for the *stop-and-wait* protocol to
know if a packet is new or is being resent.

![](images/automata_2_send.png)
![](images/automata_2_receive.png)

### Lossy channel with bit errors

Now the channel can also lose packets.

The sender is tasked with detecting and handling packet loss. In this case, if
the sender has not received an ACK in a period of time, it resends the packet
again. Now NAKs are replaced with duplicate ACKs (resending an ACK for the last
correctly received packet), this means that ACKs need to also include the
sequence number of the packed being acknowledged.

The time that the sender needs to wait in order to be sure that the packet was
lost is very difficult to estimate but it's at least the RTT plus packet
processing delays. Duplicate packets are caused by this estimate being too low.
Retransmission on the sender side can be implemented with a *countdown timer*.

![](images/automata_3_send.png)

### Pipelined Protocol

Although the last protocol is correct, the performance leaves a lot to be
desired because only one packet is sent at a time. To solve this, the sender
could be allowed to send multiple packets without waiting for ACKs for each of
them.

- Range of sequence numbers must be increased
- Buffers must be increased

Go-Back-N (GBN) Protocol
: Sliding window protocol with window size $N$. At most $N$ packets are
allowed to be sent at once. ACKs are cumulative and after a timeout event, all
unacknowledged packets are resent. This means that with a large window size,
a single packet loss can cause a large retransmission. Out of order packets are
not buffered.

![](images/go_back_n.png)

Selective Repeat (SR) Protocol
: Sliding window protocol with window size $N$. In this protocol, packed will be
ACKed and buffered weather or not they were received in order. Now each packet
must have its own timer. Packets below the window base are also acknowledged.
Problem: sender and receiver window might not be synchronized. To avoid
confusion between new and resent packets, the window size must be less than or
equal to half the size of the sequence number space.

![](images/sr_operation.png)
![](images/sr_large_window.png)

## 3.5 Connection-Oriented Transport: TCP

Before two hosts can begin to send data between each other using TCP, they must
first establish a connection. This connection is said to be a *logical
connection* unlike circuits in circuit-switched networks.

TCP provides a *point-to-point full-duplex* service. That means that data can
flow in both directions at the same time but no *multicasting* is supported.

Three-way handshake
: TCP connection establishment procedure.

Maximum Segment Size (MSS)
: Maximum amount of data that can be placed in the TCP segment (not the size of
the whole segment).

Maximum Transmission Unit (MTU)
: Largest link-layer frame that can be sent by the local sending host. Ethernet
and PPP have an MTU of 1,500 bytes.

Sequence Number
: The sequence number is part of the TCP segment header and is the byte-stream
number of the first byte in the segment.

Acknowledgment Number
: This is also part of the TCP segment header. It denotes the next byte that the
host is expecting to receive from the other system. TCP uses cumulative
acknowledgment.

Interestingly, TCP implementations are free to choose between bufferning or
dropping out of order packets.

### Estimating the RTT

For every sent packet, SampleRTT is the time between sending the packet and
receiving an ACK. This is measured approximately once every RTT but only for
packets that were sent once.

For every newly obtained SampleRTT, the EstimatedRTT is updated using
$$\text{EstimatedRTT} = (1-\alpha) \cdot \text{EstimatedRTT} + \alpha \cdot
\text{SampleRTT}$$
The recommended value for $\alpha$ is 0.125.

It is also useful to calculate the variability of the RTT.
$$\text{DevRTT} = (1-\beta) \cdot \text{DevRTT} + \beta \cdot |\text{SampleRTT}
- \text{EstimatedRTT}|$$
The recommended value for $\beta$ is 0.25.

Both are then used to determine the TCP retransmission timeout interval.
$$\text{TimeoutInterval} = \text{EstimatedRTT} + 4 \cdot \text{DevRTT}$$

It is recommended that TCP use only one timer. Once a new segment is passed to
the network layer, the timer is started (with expiration equal to
TimeoutInterval as explained above). Upon receiving an ACK with value $y$, this
value is compared to the SendBase, which is the sequence number of the oldest
unacknowledged byte. If $y >$ SendBase, then new packets are being ACKed, thus
SendBase is updated and the timer is reset if all sent packets have been ACKed.

Whenever there is a timeout, the protocol retransmits the oldest packet that has
not been ACKed.

Doubling the timeout interval every time a packet is retransmitted provides some
sort of congestion control. This is because lost packets usually mean network
congestion. If instead of exponentially increasing he timeout interval, we just
resent the packets again and again, the congestion would only get worse.

Duplicate ACKs may indicate that the segment following the one that is being
ACKed has been lost. If there are 3 duplicate ACKs, the sender performs *fast
retransmit*, that is, it sends the missing packet even before the timer runs
out.

TCP is a hybrid of GBN and SR protocols.

### Flow Control

Flow control by maintaining a variable *receive window*. This indicates the free
space available in the receive buffer. For a sending host to make sure that the
other host's receiving buffer is not being overflowed, it must ensued that
$\text{LastByteSent} - \text{LastByteAcked} \leq \texttt{rwnd}$. The receive
window is communicated from one host to another.

### Establishing a TCP Connection

1. Client sends SYN Segment with its sequence number
2. Server allocates buffers and variables and sends connection-granted
   segment and sets the ACK to the client's + 1. It puts its own sequence
   number in the packet's SEQNO.
3. Client also allocates buffers and variables and ACKs the packet with the
   server's SEQNO + 1. This packet has SYN set to 0 and may carry
   application data.

## 3.6 Principles of congestion control

One of the reason why congestion can occur is because of a shared link with
outgoing bandwidth lower than the sum of incoming traffic bandwidth. Assuming
the link has infinite buffers, as the sum increases, the average packet delay
increases without bound.

![](images/congestion_infinite_delay.png)

Without the assumption of infinite buffers, the performance could look something
like this.

![](images/congestion_infinite_buffers.png)

a. Assumes that packets are only sent when there is space in the buffer.
b. Assumes that packets are only sent when it is known for certain that the
   packet was lost.
c. Assumes that packets may also be retransmitted unnecessarily.

Congestion on multi-hop paths can sometimes be even worse because traffic being
dropped at any link along the path means that the transmission capacity of the
links before are being wasted.

![](images/congestoin_multihop.png)

### Approaches to Congestion Control

- End-to-end congestion control
    : Network layer provides no support for congestion control. This is the approach
    TCP takes.
- Network-assisted congestion control
    : The network layer provide feedback about the congestion of the network.
        - Direct feedback
        : The router sends a packet to the sender indicating congestion.
        - Indirect feedback
        : The router marks a packet being sent from sender to receiver
        indicating that the network is congested. The receiver will then inform
        the sender.

## 3.7 TCP Congestion Control

TCP uses perceived network congestion to limit the rate at which data is being
sent. To do so, the sender has a variable `cwnd` to keep track of the congestion
window such that $$\text{LastByteSent} - \text{LastByteAcked} \leq
\min\{\texttt{rwnd},\texttt{cwnd}\}$$ holds.

Loss event
: Timeout or receipt of three duplicate ACKs. This indicates to the sender that
the network might be congested. The congestion window should be decreased.

Self-clocking
: TCP uses the arrival of ACKs as indication of a non-congested network. The
congestion window can be increased. If they arrive at a slow rate, the
congestion window will be increased slowly, otherwise (if the rate is higher),
it will increase faster.

### Slow start

`cwnd` is initialized to 1 MSS and then it is grown by 1 MSS for every correctly
received ACK packet. This result in a doubling of `cwnd` every RTT.

If a timeout even occurs, cwnd is set back to 1 MSS and `ssthresh` is assigned
half the value of cwnd when the congestion was detected and *slow start* is
reentered. When `cwnd` once again
reaches `ssthresh`, TCP enters *congestion avoidance mode*.

If instead, three duplicate ACKs are received, a fast retransmit is performed
and TCP enters *fast recovery mode*.

![](images/congestion_fsm.png)

### Congestion avoidance mode

When TCP enters this state, `cwnd` is half what it was when congestion as
detected. That means that doubling `cwnd` again like in the *slow start* mode
could likely encounter congestion again. Instead, the congestion window is
increased by $\text{MSS} \cdot \frac{\text{MSS}}{\texttt{cwnd}}$ every time an ACK
is received. This causes `cwnd` to be increased by 1 MSS every RTT (instead of
being doubled).

When a timeout event occurs, this mode behaves the same as in *slow start* but
for tree duplicate ACKs, `cwnd` is set to $\frac{\texttt{cwnd}}{2}
+ 3 \text{MSS}$, `ssthresh` is set to $\frac{\texttt{cwnd}}{2}$ and then
*fast recovery mode* is entered.

### Fast recovery mode

`cwnd` is increased by 1 MSS for every duplicate ACK for the packet that was
lost (these packets don't ACK the missing packet but the one before). Once the
missing packet is ACKed, `cwnd` is halved and TCP enters *congestion avoidance
mode*.

When a timeout occurs, it is handled as in the *congestion avoidance* and *slow
start* modes.

Fast recovery is not mandatory in TCP so the early version *Tahoe* did not
implement it and just handled the triple duplicate ACKs the same as timeout
events.

![](images/tahoe_reno.png)

Additive-increase, multiplicative-decrease (AIMD)
: TCP's congestion control behaviour (increasing `cwnd` additively when there is
no congestion and decreasing it multiplicatively otherwise). It produces
a saw-tooth pattern in the congestion window size.

![](images/congestion_window_sawtooth.png)

### Fairness

Fair
: If the average transmission rate of $K$ systems all connected to the same
bottleneck link with rate $R$ is approximately $\frac{R}{K}$, then
a congestion-control mechanism is said to be fair.

![](images/fairness_bottleneck.png)

Under ideal conditions, TCP is fair for any amount of connections sharing
a bottleneck link. In a 2-connection example visualized by the graph, an
additive increase will result in the increase of both throughputs along a 45
degree line while a multiplicative increase results in a point halfway along
a line from the current point towards the origin. Eventually the point
fluctuates along the equal bandwidth line.

![](images/fairness_graph.png)

In reality, connections with lower RTT can achieve higher throughput because
they can increase their congestion window quicker. This is also made worse by
the fact that each connection on a system counts the same as an independent one,
making systems with more parallel connections achieve higher bandwidths. Also, 
the lack of fairness of UDP packets can cause TCP to be crowded out.

# 4. The Network Later: Data Plane

## 4.1 Overview of the Network Layer

**Main Network Layer Functions:**

- Forwarding
    : When a packet arrives at a router, it must be correctly forwarded through
    the appropriate output link in the direction of the destination host.
    Implemented in the data plane.
- Routing
    : Determining the route from sender to receiver through routing algorithms.
    Implemented in the control plane.

Forwarding table
: Table that is indexed using a packet's header values to determine the link
    interface to which the packet should be forwarded. The content is determined
    by the routing algorithm. In a traditional approach routers have both
    routing and forwarding capabilities but another approach would be to have
    a separate remote control plane that sends update packets to the routers.

Best-effort service
: This is the network service model that the Internet's network layer
provides.

- No guarantee of in order arrival
- No guarantee of packet delivery
- No guarantee on delay
- No guarantee on minimum bandwidth

## 4.3 The Internet Protocol (IP)

Two versions: IPv4 and IPv6.

The IP packet (segment) header contains fields with information regarding the
datagram. E.g. source and destination IP addresses, time-to-live, and checksum;
as well as a data field.

### Datagram Fragmentation

The MTU of the link-layer protocol limits the maximum size of an IP datagram. IP
datagrams bigger than the MTU are *fragmented* into smaller IP datagrams and
then encapsulated in link-layer frames. For performance and simplicity reasons,
the datagram reassembly is done in the end systems and not in the routers. IP
headers therefore include *identification*, *flag* and *fragmentation offset*
fields.

### IP Addressing

IP addresses uniquely (except the private IP range) identify interfaces on a router
or host. It is 32 bits long and it is divided into two main parts. Written in
the form `a.b.c.d/x`, the `x` most significant bits represent the network portion
(aka. *prefix*) while the remaining bits identify hosts within that network
(they may also have further subnets).

Broadcast Address
: The address `255.255.255.255` is reserved for forwarding packets to all hosts in
the same subnet.

Block addresses are assigned to organizations by their ISP these are subnets of
the ISP's address range. ISPs get their addresses from regional Internet registers
which get their addresses assigned by ICANN.

**Example:**

ISP's block:\
`200.23.16.0/20` → **` 11001000 0001011 0001|`**`0000 0000000`

Organization 0:\
`200.23.16.0/23` → **` 11001000 0001011 0001000|`**`0 0000000`

Organization 1:\
`200.23.18.0/23` → **` 11001000 0001011 0001001|`**`0 0000000`

Organization 2:\
`200.23.20.0/23` → **` 11001000 0001011 0001010|`**`0 0000000`

### Dynamic Host Configuration Protocol (DHCP)

DHCP allows a host to be allocated an IP address inside a network automatically.
Useful especially if hosts are connected and reconnected frequently or new
hosts are added. For this, a DHCP server must be present in the same subnet.
Otherwise a relay agent can forward the DHCP request to the correct subnet. The
following steps are required for a host to get an IP address:

1. DHCP server discovery
    : Client sends UDP packet with destination address
    `255.255.255.255` and source address `0.0.0.0`.
2. DHCP server offer
    : The server replies to the broadcast address with a proposed IP
    address, the network mask, transaction ID and the *address lease time*.
    The client might be able to choose between multiple addresses if the re
    are multiple DHCP servers in the network and both send a proposal.
3. DHCP request
    : Client echoes back the chosen server's parameters.
4. DHCP ACK
    : Server confirms the parameters and client can use it's newly assigned
    address.

### Network Address Translation (NAT)

NAT avoids having to assign a unique global IP to every network device that is
connected to the Internet (this would not even be possible in IPv4). This is
done by translating public IP addresses and port numbers to private IP and
port numbers with a *NAT translation table*. There are multiple address spaces
reserved for use in private networks so they cannot be used on the global internet.

To the rest of the Internet, a NAT router looks like a single host. This breaks
direct communication from an outside host to a host behind the NAT if no
connection in the opposite direction was made before. It also violates the
principle that routers should only process packets up to network layer because
they modify IP addresses as well as port numbers.

### IPv6

IPv6 was developed as a response to the large number of hosts on the Internet
and therefore the lack of available IPv4 addresses.

**Important Changes**:

1. Expanded addressing capabilities
    : IP address extended from 32 to 128 bis. Anycast address was added so that
    a packet can be delivered to any one of a group of hosts.
2. Streamlined 40-byte header
    : Some IPv4 header fields so that the resulting 40-byte fixed-length header
    can be processed faster.
3. Flow labeling
    : Some packets treated as flow to be handled differently (e.g. video
    streaming, high priority users, etc.).

Fragmentation/assembly must be handled at the endpoints. If a sent datagram is
too large, the router will send back an error packet instead of performing
fragmentation.

Additionally, the header checksum was removed and the optional fields are
pointed to instead of being part of the standard header, making the header
fixed-length.
