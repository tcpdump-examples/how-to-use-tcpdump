

how-to-use-tcpdump
===========================


Tcpdump command is a famous network packet analyzing tool that is used to display TCP\IP & other network packets being transmitted over the network attached to the system on which tcpdump has been installed. Tcpdump uses libpcap library to capture the network packets & is available on almost all Linux/Unix flavors.


`Linux Tcpdump: Filter ipv6 ntp ping packets <https://www.howtouselinux.com/post/linux-tcpdump-filter-ipv6-ntp-ping-packets>`

[Tcpdump: capture DHCP & DHCPv6 packets](https://www.howtouselinux.com/post/tcpdump-capture-dhcp-dhcpv6-packets)

[20 Advanced Tcpdump Examples On Linux](https://www.howtouselinux.com/post/20-tcpdump-advanced-examples-on-linux)

[10 Useful tcpdump command examples](https://www.howtouselinux.com/post/10-useful-tcpdump-command-examples)

[Filtering ICMP ICMPv6 Packets with Tcpdump](https://www.howtouselinux.com/post/tcpdump-filter-icmpv6-packets)

[Tcpdump: Filter Packets with Tcp Flags](https://www.howtouselinux.com/post/tcpdump-capture-packets-with-tcp-flags)

[Tcpdump Cheat Sheet (Basic Advanced Examples)](https://www.howtouselinux.com/post/tcpdump-cheat-sheet)

[Filtering SSH Packets with Tcpdump](https://www.howtouselinux.com/post/debugging-ssh-packets-with-tcpdump)

[Filtering DNS with Tcpdump](https://www.howtouselinux.com/post/tcpdump-filter-dns-packets)

[Filtering CDP LLDP packets with Tcpdump](https://www.howtouselinux.com/post/capture-cdp-or-lldp-packets-with-tcpdump-on-linux)

tcpdump
=======

.. contents:: :depth: 3

Network packet capture tool in the CLI. `tcpdump` is extremely useful
for quick debugging of network issues. Use the `pcap-filter` manpage for
the filter syntax reference.

Flags
-----

*tcpdump* Flags:

  ========  ============  =============================================
  TCP Flag  tcpdump Flag  Meaning
  ========  ============  =============================================
  SYN       S             Syn packet, a session establishment request.
  ACK       A             Ack packet, acknowledge sender's data.
  FIN       F             Finish flag, indication of termination.
  RESET     R             Reset, indication of immediate abort of conn.
  PUSH      P             Push, immediate push of data from sender.
  URGENT    U             Urgent, takes precedence over other data.
  NONE      A dot *.*     Placeholder, usually used for ACK.
  ========  ============  =============================================

Examples
--------

Capturing ARP Traffic
^^^^^^^^^^^^^^^^^^^^^

When using tcpdump to capture ARP, make sure to dump the hex output (*-X*)
and also decode ethernet header using (*-e*). **Note: Use *-XX* to also
show ethernet header dump**.

.. code-block:: bash

    $ sudo tcpdump -nnvvv -e -X arp 
      tcpdump: listening on wlan0, link-type EN10MB (Ethernet), capture size 262144 bytes
      20:01:28.452956 48:5a:b6:51:57:dd > ff:ff:ff:ff:ff:ff, ethertype ARP (0x0806), length 60: Ethernet (len 6), IPv4 (len 4), Request who-has 192.168.1.1 tell 192.168.1.23, length 46
          0x0000:  0001 0800 0604 0001 485a b651 57dd c0a8  ........HZ.QW...
          0x0010:  0117 0000 0000 0000 c0a8 0101 0000 0000  ................
          0x0020:  0000 0000 0000 0000 0000 0000 0000       ..............
      20:01:28.454472 bc:ee:7b:58:17:b8 > 48:5a:b6:51:57:dd, ethertype ARP (0x0806), length 42: Ethernet (len 6), IPv4 (len 4), Reply 192.168.1.1 is-at bc:ee:7b:58:17:b8, length 28
          0x0000:  0001 0800 0604 0002 bcee 7b58 17b8 c0a8  ..........{X....
          0x0010:  0101 485a b651 57dd c0a8 0117            ..HZ.QW.....

Capturing Traffic on Localhost
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

During development, there is usually a local webserver setup in
`http://localhost`. Custom apps/scripts are tested against this local
webserver to make them functionally correct. Thus, it is important to be
able to analyze traffic to and from the local webserver. Using the local
webserver for traffic analysis helps as there are no external traffic
that will confuse the analysis.

To capture `localhost` traffic:

.. code-block:: bash

    sudo tcpdump -A -v --number -i lo tcp port http

* `-A` is used to decode protocol in `ASCII`.
* `-v` is used for verbose mode. This allows us to see `tcp` communication details (flags, sequence numbers, etc).
* `--number` denomitate the packets
* `-i lo` use local loopback interface
* `tcp port http` the filter specifying protocol and port to use for capture.

Use `-l` for line buffering to see data while capturing it to a file.

.. code-block:: bash

    sudo tcpdump -l -A -v --number -i lo tcp port http | tee /tmp/capture

Capturing GMail Traffic
^^^^^^^^^^^^^^^^^^^^^^^

GMail goes over IMAP but not the standard IMAP port (143), it uses 993:

.. code-block:: bash

    sudo tcpdump -vvv -X --number -i wlan0 host 192.168.1.24 and tcp port 993

Use ``-vvv`` (three is max) to decode max level of the packets. Then use
*-X* to decode in Hex and ASCII.

Dropped Packets by the Kernel
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

tcpdump uses a little buffer in the kernel to store captured packets. If
too many new packets arrive before the user process tcpdump can decode
them, the kernel drops them to make room for freshly arriving packets.

Use *-B* to increase the buffer. This is in units of KiB (1024 bytes).

Capturing TCP SYN Packets
^^^^^^^^^^^^^^^^^^^^^^^^^

To capture SYN packets only:

.. code-block:: sh

    $ sudo tcpdump -nnvvv host 192.168.1.116 and "tcp[tcpflags] & tcp-syn != 0"

To capture TCP keepalive packets 1-byte or 0-byte ACKs. Note that a
keepalive probe is a packet with no data and ACK flag turned on:

.. code-block:: sh

    $ sudo tcpdump -vv "tcp[tcpflags] == tcp-ack and less 1"

Capture Outgoing SSH Traffic
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: sh

    $ sudo tcpdump -nn src 192.168.1.116 and tcp port 22

Get Time Delta Between Request/Response
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Pass the *-ttt* flag to get the time delta between current line and
previous line.

.. code-block:: bash

    $ sudo tcpdump -nS -ttt port http and host snapshot.debian.org

    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes

    00:00:00.000000 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [S], seq 1140376233, win 29200, options [mss 1460,sackOK,TS val 22265623 ecr 0,nop,wscale 7], length 0
    00:00:00.228373 IP 193.62.202.30.80 > 192.168.1.170.34233: Flags [S.], seq 1460190713, ack 1140376234, win 5792, options [mss 1350,sackOK,TS val 74072844 ecr 22265623,nop,wscale 7], length 0
    00:00:00.000040 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [.], ack 1460190714, win 229, options [nop,nop,TS val 22265680 ecr 74072844], length 0
    00:00:00.000119 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [P.], seq 1140376234:1140376399, ack 1460190714, win 229, options [nop,nop,TS val 22265680 ecr 74072844], length 165
    00:00:00.222658 IP 193.62.202.30.80 > 192.168.1.170.34233: Flags [.], ack 1140376399, win 54, options [nop,nop,TS val 74072902 ecr 22265680], length 0
    00:00:00.001001 IP 193.62.202.30.80 > 192.168.1.170.34233: Flags [P.], seq 1460190714:1460191405, ack 1140376399, win 54, options [nop,nop,TS val 74072902 ecr 22265680], length 691
    00:00:00.000032 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [.], ack 1460191405, win 239, options [nop,nop,TS val 22265736 ecr 74072902], length 0
    00:00:00.008210 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [F.], seq 1140376399, ack 1460191405, win 239, options [nop,nop,TS val 22265738 ecr 74072902], length 0
    00:00:00.183523 IP 193.62.202.30.80 > 192.168.1.170.34233: Flags [F.], seq 1460191405, ack 1140376400, win 54, options [nop,nop,TS val 74072960 ecr 22265738], length 0
    00:00:00.000060 IP 192.168.1.170.34233 > 193.62.202.30.80: Flags [.], ack 1460191406, win 239, options [nop,nop,TS val 22265784 ecr 74072960], length 0

Capturing WiFi Packets
^^^^^^^^^^^^^^^^^^^^^^

First, the `wlan0` interface needs to be set to monitor mode:

.. code-block:: bash

    $ sudo ifconfig wlan0 down
    $ sudo iwconfig wlan0 mode Monitor
    $ sudo ifconfig wlan0 up

Then, run `tcpdump` with the following flags:

.. code-block:: bash

    $ sudo tcpdump -I -i wlan0 -w thermostat.pcap -e -s 0 ether host 00:d0:2d:xx:xx:xx

This captures all packets originating from the Honeywell thermostat for example.



Nfsiostat
===========================
https://www.howtouselinux.com/post/use-linux-nfsiostat-to-troubleshoot-nfs-performance-issue

Linux command nfsiostat can be used to determine the average Round Trip Time (avg RTT) in milliseconds which is a good measurement for storage performance issue. The nfsiostat command is available in later versions of the nfs-utils package.

