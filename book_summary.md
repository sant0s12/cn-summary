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
+ 3 \text{MSS}$ (the slides omit adding $3$ MSS), `ssthresh` is set to $\frac{\texttt{cwnd}}{2}$ and then
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

# 5. The Network Layer: Control Plane

## 5.2 Routing Algorithms

Goal: Find cheap paths from senders to receivers also taking policies into
account. Graphs are usually used to formulate the problem with edge cost
determining some kind of important measure like distance, speed or monetary
cost.

Centralized routing algorithm (Link-State)
: An algorithm that is aware of all links and their connections (with their
respective costs) computes shortest paths between source and destination either
on a central controller or replicated on each router. An example of such an
algorithm is *Dijkstra's algorithm*. It computes the least cost path from a source
to all other nodes in the network, thus, when a router finishes the computation,
it can know the next hop for a given end destination. In some situations (e.g.
when the path cost is based on network traffic), this algorithm can cause
oscillations. This can be fixed by randomizing the time at which routers send
link advertisements so that not all routers run the same instance of the
algorithm.

Decentralized routing algorithm
: No node has complete information about the network so cheapest paths are
calculated iteratively by sending and receiving information to and from
neighbouring routers.

Static routing algorithms
: Routes change slowly over time, often because of human interaction.

Dynamic routing algorithm
: Routes change dynamically according to changes in topology or traffic. More
responsive to network changes but can suffer problems like loops or route
oscillation.

### Distance-Vector (DV) Algorithm

This algorithm is an example of a distributed algorithm. It is a distributed
version of the *Bellman-Ford* algorithm.

Each node $x$ keeps track of the following routing information:

- $c(x, v)$: The patch cost from $x$ to $v$, $v$ being a directly connected to
  $x$.
- $D_x = [D_x(y) : y \in N]$: The node's distance vector. $D_x(y)$ being an
  estimate of the least cost path from $x$ to $y$ and $N$ being the set of
  nodes.
- $D_v = [D_v(y) : y \in N]$: The distance vector for each of its neighbors $v$.

Now each node will send its distance vector to all of its neighbors
asynchronously. When a node receives such a vector, its own vector is updated
and resent to all the neighbors (if there were any changes):

$$D_x(y) = \min_{v \in \text{neighbors of } x}\{c(x,v) + D_v(y)\}$$ for all $y
\in N$. $v^*(y)$ is then the router that achieves the minimum for a given $y$ and
is selected as the next hop router for that destination.

Eventually (as long as the asynchronous communication is not interrupted)
$D_x(y)$ will converge to the actual cost of the least cost path.

When the link cost changes, the distance vector is recomputed and sent.
Decreases in cost are propagated quickly while increases are propagated slowly
and can lead to the *count-to-infinity* problem. This can be mitigated (but not
fixed for the general case) by *poisoned reverse*. It consists of the following:
If a router $z$ routes through $y$ to get to $x$, it will advertise to $y$ that
its distance to $z$ is $\infty$.

## 5.3 Insta-AS Routing in the Internet: OSPF

In reality, the DV algorithm cannot be applied on a global scale due to the
large about of network routers (large overhead) and the fact that the Internet
is a network of network, and as such, ISPs usually want to run their network as
they please.

Therefore routers are usually clustered into *Autonomous Systems (ASs)*, that
is, a group of routers that are administered by the same entity. Each AS has
their own globally unique AS number (ASN) assigned by ICANN.

Intra-autonomous system routing protocol
: Routing algorithm that runs within an AS.

In this protocol, each router has a complete graph of the whole AS and then runs
Dijkstra's algorithm to computer the shortest paths to all subnets (from the
current router). Edge weights must be set manually.

OSPF broadcasts information to *all* other routers on link-state change and also
periodically. This information is sent via IP so the reliable transport capacity
must also be implemented.

## 5.4 Routing Among the ISPs: BGP

Border Gateway Protocol (BGP) is the intra-autonomous system routing protocol
that all ISPs run and it has the following tasks:

1. Obtain prefix reachability information from ASs
    : Allows advertising of each subnet to the rest of the internet.
    Reachability information is received from neighboring ASs.
2. Determine the *best* routes to the prefixes
    : Best route to a given prefix based on policy as well as reachability
    information.

### Advertising BGP Route Information

Internal router
: Connects only to hosts and routers within the AS.

Gateway router
: Connects to one or more routers in other ASs.

BGP routers are connected with each other via TCP (called BGP connection).

eBGP
: BGP connection spanning two ASs.

iBGP
: BGP connection between routers within the same AS.

![](images/eBGP_iBGP.png)

**Example advertisement of prefix x:**

1. 3a sends eBGP message `"AS3 x"` to 2c.
2. 2c sends iBGP message `"AS3 x"` to all routers within AS2.
3. 2a sends eBGP message `"AS2 AS3 x"` to 1c.
4. 1c sends iBGP message `"AS2 AS3 x"` to all routers within AS1.

### Determining the Best Routes

Problem: Many paths from a router to a given destination subnet.

Route
: Prefix along with its *BGP attributes*. This is what is advertised across BGP
connections.

Important BGP attributes:

- AS-PATH
    : List of ASs through which the advertisement has passed (see above
    example). The AS that passes the advertisement along prepends its own ASN
    to the existing AS-PATH.

- NEXT-HOP
    : IP address of the router interface that begins the AS-PATH.

Hot potato routing
: Chose the BGP route with the least cost to the NEXT-HOP router. The cost to
the NEXT-HOP routers is provided by the intra-AS routing protocol. This
algorithm is selfish as it does not consider the cost along the whole path but
only inside the current AS.

![](images/BGP_adding_AS.png)

If there are two or more routes to the same prefix, the following rules are
used (if two routes have the same value for any of them, the next one will be
used:

1. Local preference
    : Can be set by the router or leaned by another route in the same AS. Routes
    with higher values will be selected.
2. Shortest AS-PATH
    : If this was the only rule, BGP would be an AS-level DV algorithm.
3. Closest NEXT-HOP
    : Hot potato routing.
4. BGP identifiers

### IP-Anycast

This mechanism is used for being able to replicate servers (DNS, CDNs, etc.)
while pointing the client to the best (nearest) server.

This is done by having multiple servers advertise the same IP address. Thanks to
BGP routing, the best route for each client will be chosen. However, this may
cause TCP packets destined to one server to arrive at another, which would make
for a bad user experience. Because DNS does not rely on TCP connections, this is
very popular for pointing clients to the closest DNS server.

![](images/BGP_CDN.png)

### Routing Policy

The local-preference attribute is the primary factor used for route selection.
This is fixed by the policy of the local AS.

Multi-homed access ISP
: Connected to the rest of the network via more than one provider.

Policy is based on commercial relationships between different ISPs (detailed in
the slides IIRC).

## ICMP:  The Internet Control Message Protocol

This protocol is used by hosts and routers to communicate network information
with each other, usually error messages. ICMP is not part of IP but an
upper-layer protocol. They contain type and code fields as well as part of the
IP datagram that caused the ICMP message to be generated.

The `ping` program as well as `traceroute` are implemented using ICMP. `ping`
just sends an ICMP echo request and gets an echo back. `traceroute` on the other
hand sends UDP messages with increasing TTLs such that when the n-th packet
arrives at the n-th router, the TTL will indicate that the packet should not be
forwarded on. This causes the router to send back an ICMP alert message
(containing information about the router) to the origin and this is picked up by
`traceroute`.

# 6. The Link Layer and LANs

## 6.1 Introduction to the Link Layer

Node
: Device that runs a link-layer protocol (e.g. hosts, routers. switches, WiFi
access points, etc.).

Link
: Communication channel that connects adjacent nodes.

**Possible services provided by the link layer:**

- Framing
    : As in the upper layers, encapsulation might also be available in the link
    layer. In this case, network datagrams are encapsulated in link-layer frames
    with some added header fields.

- Link access
    : Coordinate frame transmissions onto the broadcast link via a *medium
    access control (MAC)* protocol. Especially interesting when the link is
    shared by many nodes and not just two.

- Reliable delivery
    : Guarantees reliable delivery of frames between nodes. Often used for
    unreliable links but might be considered as unnecessary overhead.

- Error detection and correction
    : Errors might be caused by physical interferences. The algorithms
    are usually implemented in hardware.

Most of the network layer is implemented in hardware inside *network interface
cards (NICs)*, although some of the higher-level link-layer functions are
implemented in software. This is the part of the protocol stack where software
and hardware meet.

![](images/nic.png)

## 6.2 Error-Detection and -Correction Techniques

There is a trade-off between smaller probability of undetected errors and
overhead.

### Parity Checks

![](images/parity.png)

Simplest form: Adding an extra *parity bit* and setting it such that the number
of 1s is either even or uneven (depending on the scheme). When a message is
received the number of 1s is counted and checked against the schema to detect
errors. Problem: Even number of bit flips causes undetected errors.

This can be generalized into two dimensions where the $d$ message bits are divided
into $i$ rows and $j$ columns. Then parity bits are computer for each row and
column (including the parity columns). Now single bit flips can be corrected and
two bit flips can be detected.

![](images/parity_2d.png)

Forward error correction (FEC)
: Ability of the receiver to both detect and correct errors. It avoids having
to wait on a retransmission in case there is an error.

### Checksums

Interpret the $d$ data bits as $k$ bit integers and add them up. Use the
resulting sum as error-detection bits. This is what the *Internet checksum* is
based on (explained in [Section 3.3][3.3 Connectionless Transport: UDP]).

This is a simple mechanism with little overhead so it is a good choice if it is
to be implemented in software (this is the case for the higher layers).

### Cyclic Redundancy Check (CRC)

Unlike software implementations of error correction, hardware implementations
can be more complex and robust.

The sender adds an extra $r$ bits to the data such that the $r+d$-bit pattern is
divisible by a generator $G$ of $r+1$ bits. If the receiver performs this
division and the remainder is not zero it will know that there is an error.

![](images/crc_calculation.png)

For the standard defined generators, consecutive bit errors of $r$ bits or fewer
will be detected.

## 6.3 Multiple Access Links and Protocols

### Random Access Protocols

In this type of protocol, nodes always transmit at their full rate unless they
detect a collision. If such a collision event happens, the nodes will retransmit
the frame after waiting a random amount of time until the frame is sent without
collision.

### Slotted ALOHA

- All frames are $L$ bits long.
- Time divided into slots of size $\frac{L}{R}$ seconds (the time to transmit
  one frame).
- Transmissions are only started at the beginning of slots.
- Nodes synchronized so that they know when the slots begin.
- Collisions are detected before the slots end.

Slotted ALOHA operation:

- Node waits until the beginning of the next slot to transmit a frame.
- If no collision happened then the transmission was successful.
- If a collision happened then the fragment is retransmitted in each subsequent
  slot with probability $p$ until no collision is detected.

This protocol is still not optimal when there is more than one node because of
collisions and empty slots due to the transmission probability.

Efficiency (in slotted protocols)
: Long-run fraction of successful slots with multiple nodes actively sending
frames.

The maximum efficiency for this protocol is 37%. This is given by the equation
$Np(1-p)^{N-1}$ for a number of nodes $N$ and retransmisison probability $p$.

### ALOHA

ALOHA works like slotted ALOHA but without slots. This means that nodes can
begin transmission right after receiving a datagram from the network layer. If
a collision is detected, the node will wait a frame send time and retry
a retransmission with probability $p$ until there is no collision. This makes
this protocol half as efficient as slotted ALOHA.

### Carrier Sense Multiple Access (CSMA)

The ALOHA protocol is unaware of the activity of other nodes. This is not the
case in CSMA.

Carrier sensing
: Node listens on the channel it wants to send and if there is transmission
happening, it will wait until it is over.

Collision detection
: Node listens to channel while its transmitting and if it senses some other
node trying to transmit on the same channel it will stop and retry (including
sensing) after some random delay.

The propagation delay also plays a role because a node could start transmitting
before the transmission of another node arrives, therefore thinking that the
channel was free.

![](images/csma_collision.png)

This shows that the nodes do not implement collision detection, they continued
transmitting even though the transmissions where colliding.

An extension of this protocol includes that collision detection.

![](images/csma_collision_avoidance.png)

**CSMA/CD (Collision Detection) Operation:**

1. Node gets datagram from the network layer and puts the generated link-layer
   frame in the buffer.
2. If the channel is busy, the node waits until it is free, otherwise it starts
   the transmission.
3. While transmitting, the node monitors the channel for other transmissions.
4. If the frame is transmitted without collision then it was successful,
   otherwise (if another transmission was detected), it stops transmitting.
5. If the transmission was aborted, the node waits a random about of time and
   retries from step 2.

The random amount of time to wait before a retry is defined for example by
*binary exponential backoff* (which is used in Ethernet). For a frame that has
experienced $n$ collisions, a value from $[0, 2n-1]$ is randomly chosen as delay.

The efficiency of CSMA/CD is $11+5 \cdot \frac{d_{\text{prop}}}{d_{\text{trans}}}$
with $d_{\text{prop}}$ being the maximum propagation delay between any two nodes
and $d_{\text{prop}}$ being the time to transmit a maximum-size frame.

## 6.4 Switched Local Area Networks

MAC Address
: Link-layer address that is associated with a network interface. Link-layer
switches do not have MAC addresses. No two adapters can have the same address.
This address is static (does not change if the network is changed) and not
hierarchical. There is a special *broadcast address* so that all the devices on
the LAN receive a frame.

Address Resolution Protocol (ARP)
: This protocol serves to translate between MAC and IP addresses. It only works
for devices in the same subnet. To do this, each host maintains a translation
table between MAC and IP addresses. To populate the table, hosts send out an
*ARP packet* to the broadcast address containing the source IP address and MAC
as well as the IP address that is to be resolved. Hosts then check their own IP
address against the requested one and if they match then they reply with their
MAC address. The querying host can then update its mapping table.

For a frame to be sent across subnets, it must be first sent to the next-hop
router. The MAC address of the router's link that is connected to the same
subnet as the sending host is discovered using ARP. Then a frame is sent out to
the LAN and processed by the router. Because the network datagram contains the
designation's host IP address, ARP is used again to discover its MAC address.
Then it's send over the LAN as usual.

### Ethernet

Widely used for wired LAN. It started out as a broadcast LAN where all frames
travelled to all adapters connected to the same LAN and used CSMA/CD. This was
later switched out for network switches.

While CRC checks are performed, there are neither positive nor negative
acknowledgment frames sent back. When CRC fails, the frame is just dropped. The
transport is thus unreliable.

### Switches

Filtering
: Determining if a frame should be dropped or forwarded.

Forwarding
: Determining the interface to which a frame should be forwarded and then doing
so.

Switch table
: Table mapping MAC addresses to the interfaces that lead to them. Contains
addresses form devices on the same LAN but not necessarily all of them.

**Operation for arriving frame on switch interface x:**

1. There is no entry for the destination MAC address of the frame: The frame
   will be forwarded to all interfaces (broadcast).
2. There is a mapping from the destination MAC address to the interface x. Then
   there is no need to forward the frame as it already comes form x.
3. There is a mapping from the destination MAC address to an interface other
   than x. Then the frame is forwarded to just that interface.

**Self learning:**

1. Table is initially empty.
2. For each frame received, the switch adds a mapping from the source MAC
   address to the interface on which the frame was received as well as the
   time when it arrived.
3. Addresses are deleted from the table after the *aging time* if no frames
   where received from that MAC.

**Advantages over broadcast links:**

1. Elimination of collisions
    : 
2. Heterogeneous links
    : Switch isolate links from each other so some switches
   may implement different media or speeds on different links.
3. Management
    : Detecting malfunctioning adapters, more localized physical
   failure, statistics gathering, etc.

![](images/interconnection_comparison.png)

# 7. Wireless and Mobile Networks

## 7.2 Wireless Links and Network Characteristics

Differences between wired and wireless:

- Decreasing signal strength
    : Signal strength decreases as it passes through objects or the distance
    increases.
- Interference from other sources
    : Other transmitting devices as well as general environmental noise can
    cause interference.
- Multipath propagation
    : Reflections off objects or the ground cause the waves to reach the
    receiver through different length paths causing the blurring of the received
    signal.

Signal-to-noise ration (SNR)
: Relative measure of the strength of the received signal and other background
noise.

Hidden terminal problem
: Two transmitting hosts A and B cannot detect each other due to attenuation or
blockage but their signals cause interference at a third host C.

### CDMA

Each bit being sent is encoded by multiplying it by a signal (*code*) that changes at
a much faster rate (*chipping rate*).

![](images/cdma_1.png)

The diagram above assumes no interference but this mechanism also works with
multiple senders if the codes are carefully chosen and under the assumption that
the interference is additive.

## 7.3 WiFi: 802.11 Wireless LANs

802.11 uses a random access protocol known as CSMA with collision avoidance
(CSMA/CA). This protocol does not implement collision detection because it is
expensive to build wireless devices that support that feature. Also, it would
not be able to detect all collisions due to the *hidden terminal problem*. Also,
it implements link-layer acknowledgments: When a frame passes the CRC test, the
receiver waits a short period of time and then sends back an ACK. If the sender
did not get an ACK after a given about of time, it will retransmit the frame up
to a defined amount of times.

**CSMA/CA operation:**

1. If channel is idle, send frame after short time.
2. Otherwise random exponential backoff.
3. If channel idle, send the whole frame and wait for ACK.
4. If ACK is received, step to is entered with the next frame. Otherwise backoff is
    reentered with a larger value interval.

**Solving the hidden terminal problem:**

By using *Request to Send (RTS)* and *Clear to Send (CTS)* control frames,
stations can reserve channels for transmission.

RTS packets are sent to the receiver when a host wants to send data. It
indicates the time required to transmit the desired data. When the RTS is
received, a CTS frame is sent by the receiver giving permission to send and
warning other hosts of the transmission and its duration.

![](images/csma_hidden_terminal.png)
