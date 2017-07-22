# Exercises for Hands-on 5: Traceroute

# Round Trip Times

In the following questions, you are asked to use the `ping` utility to measure the round trip times to several hosts on the Internet.

For the following hosts, send 10 packets, each with a length of 56 data bytes. Note: You may find that the packet responses are 64 bytes instead of 56 bytes. Look at [RFC 792](http://www.ietf.org/rfc/rfc0792.txt) to find out the reason. (Hint: Read what the `-c` flag does on `man ping`.)

```
lids.mit.edu
stanford.edu
www.sydney.edu.au
www.aoyama.ac.jp
```

1. Indicate what percentage of packets sent resulted in a successful response. For the packets from which you received a response, write down the minimum, average, and maximum round trip times in milliseconds. Note that ping reports these times to you if you tell it how many packets to send on the command line.
2. Explain the differences in minimum round trip time to each of these hosts.
3. Now send pings with 56, 512, 1024 and 2056 byte packets to `lids.mit.edu`. (Hint: read about the `-s` flag.) Write down the average round trip times in milliseconds for each of the four groups. Do the same for `www.aoyama.ac.jp`. Explain the changes in round-trip times that you observed (or didn't observe) in both cases.

*Write or type your answers to questions 1-3 below. Your answers should not extend onto a second page.*

1. Results for `lids.mit.edu`:
    ```
    --- lids.mit.edu ping statistics ---
    10 packets transmitted, 9 packets received, 10.0% packet loss
    round-trip min/avg/max/stddev = 213.924/258.681/329.680/44.787 ms
    ```
    Results for `stanford.edu`:
    ```
    --- stanford.edu ping statistics ---
    10 packets transmitted, 10 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 179.998/188.801/220.945/11.443 ms
    ```
    Results for `www.sydney.edu.au`:
    ```
    --- www.sydney.edu.au ping statistics ---
    10 packets transmitted, 10 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 278.548/314.968/387.438/38.077 ms
    ```
    Results for `www.aoyama.ac.jp`:
    ```
    --- www.aoyama.ac.jp ping statistics ---
    10 packets transmitted, 10 packets received, 0.0% packet loss
    round-trip min/avg/max/stddev = 70.699/73.339/81.888/3.353 ms
    ```
2. The difference must be geographically-based. I am currently in Daejeon, South Korea, and as such, pinging Japan is the fastest, while pinging Australia is the slowest. However, Cambridge is farthest from South Korea, but these results indicate that it may also be network quality, as MIT is still faster.
3. `lids.mit.edu`:
   ```
   56: round-trip min/avg/max/stddev = 217.945/258.516/324.202/33.210 ms
   512: round-trip min/avg/max/stddev = 217.443/235.609/273.243/18.375 ms
   1024: round-trip min/avg/max/stddev = 214.223/260.954/319.290/38.338 ms
   2056: 10 packets transmitted, 0 packets received, 100.0% packet loss
   ```
   `www.aoyama.ac.jp`:
   ```
   56: round-trip min/avg/max/stddev = 69.869/71.780/78.970/2.521 ms
   512: round-trip min/avg/max/stddev = 70.179/71.516/73.368/1.028 ms
   1024: round-trip min/avg/max/stddev = 70.565/72.517/79.287/2.380 ms
   2056: round-trip min/avg/max/stddev = 73.541/73.599/73.657/0.058 ms
   ```
   It appears that for larger packet sizes, networks further away from the user's current location have more trouble processing. MIT couldn't handle my requests from here in Korea once we reached 2056 bytes. Meanwhile, Japan handled every packet size with approximately the same travel times (except with 80% packet loss when dealing with 2056 bytes).

# Unanswered Pings
For the following hosts, send 50 packets that have a length of 128 data bytes. Indicate what percentage of the packets resulted in a successful response.
```
www.wits.ac.za (University of the Witwatersrand, Johannesburg)
www.apple.com
```
4. For some of the hosts, you may not have received any responses for the packets you sent. What are some reasons as to why you might have not gotten a response? (Be sure to check the hosts in a web browser.)

*Write or type your answers to question 4 below. Your answers should not extend onto a second page.*

4. `wits.ac.za` received none of my packets. `apple.com` received 100% of my packets. I suspect that African servers may have more security and less tolerance for outside packets coming in. Whether this is a routing-specific security measure or not is beyond me.

# Understanding Internet Routes Using traceroute
As the name implies, `traceroute` essentially allows you to trace the entire route from your machine to a remote machine. The remote machine can be specified either as a name or as an IP address.

We include a sample output of an execution of `traceroute` and explain the salient features. The command `traceroute www.google.com` tries to determine the path from the source machine to `www.google.com`. The machine encountered on the path after the first hop is `NW12-RTR-2-SIPB.MIT.EDU`, the next is `EXTERNAL-RTR-1-BACKBONE-2.MIT.EDU`, and so on. In all, it takes 13 hops to reach `py-in-f99.google.com`. The `man` page for `traceroute` contains explanations for the remaining fields on each line.

```bash
> traceroute www.google.com
traceroute: Warning: www.google.com has multiple addresses; using 64.233.167.99
traceroute to www.l.google.com (64.233.167.99), 30 hops max, 40 byte packets
 1  NW12-RTR-2-SIPB.MIT.EDU (18.181.0.1)  0.476 ms  0.318 ms  0.237 ms
 2  EXTERNAL-RTR-1-BACKBONE-2.MIT.EDU (18.168.1.18)  0.827 ms  0.624 ms  0.753 ms
 3  EXTERNAL-RTR-2-BACKBONE.MIT.EDU (18.168.0.27)  1.097 ms  0.772 ms  0.887 ms
 4  207.210.142.233 (207.210.142.233)  0.578 ms  0.549 ms  0.713 ms
 5  207.210.142.1 (207.210.142.1)  0.750 ms  2.530 ms  1.178 ms
 6  207.210.142.2 (207.210.142.2)  5.886 ms  15.387 ms  5.762 ms
 7  64.57.29.21 (64.57.29.21)  24.732 ms  24.693 ms  24.695 ms
 8  72.14.236.215 (72.14.236.215)  31.733 ms  27.588 ms 216.239.49.34 (216.239.49.34)  27.810 ms
 9  66.249.94.235 (66.249.94.235)  12.495 ms 209.85.252.166 (209.85.252.166)  36.961 ms  26.459 ms
10  216.239.46.224 (216.239.46.224)  33.736 ms  33.396 ms 209.85.248.221 (209.85.248.221)  26.130 ms
11  66.249.94.133 (66.249.94.133)  26.126 ms 72.14.232.53 (72.14.232.53)  25.744 ms  25.611 ms
12  66.249.94.133 (66.249.94.133)  26.183 ms  27.460 ms 72.14.232.70 (72.14.232.70)  37.800 ms
13  py-in-f99.google.com (64.233.167.99)  28.249 ms  26.050 ms  26.398 ms
```

5. In at most 50 words, explain how `traceroute` discovers a path to a remote host. The man page might be useful in answering this question.

*Write or type your answers to question 5 below. Your answers should not extend onto a second page.*

5. `traceroute` sends probe packets whose jobs are to hop around the network until the target address is reached, at which point we know we are finished. These probe packets are designated with a time to live (`ttl`).

# Routine Asymmetries
For this exercise, you need to use the traceroute server at http://www.slac.stanford.edu/cgi-bin/nph-traceroute.pl. You'll use this server to execute a traceroute to your own machine.

To figure out your machine's IP address, run `/sbin/ifconfig`. You'll get a lot of information, including its IP. Once you have your IP, use Stanford's server to execute a `traceroute` to it. Then, run your own `traceroute` to Stanford's server, via

```bash
> traceroute [IP ADDRESS FROM STANFORD]
```

You can get Stanford's IP address from the website above. If you use a different server, make sure that you note in your hands-on that you used a different server than the question asked for.

6. Show the output of traceroute from each direction above.
7. Describe anything unusual about the output. Are the same routers traversed in both directions? If not, why might this happen?

*Write or type your answers to questions 6 and 7 below.*

6. Running from Stanford to my Athena SSH, the results are below.

    ```bash
    Executing exec(traceroute -m 30 -q 3 18.9.64.27)
    traceroute to 18.9.64.27 (18.9.64.27), 30 hops max, 40 byte packets
     1  134.79.197.131 (134.79.197.131)  0.944 ms  0.706 ms  0.670 ms
     2  rtr-core2-p2p-serv01-02.slac.stanford.edu (134.79.253.253)  0.646 ms  0.576 ms  0.674 ms
     3  rtr-fwcore2-trust-p2p-core2.slac.stanford.edu (134.79.254.146)  0.887 ms  0.837 ms  0.668 ms
     4  rtr-core2-p2p-fwcore2-untrust.slac.stanford.edu (134.79.254.149)  1.159 ms  1.096 ms  1.237 ms
     5  * * *
     6  sunncr5-ip-c-slac.slac.stanford.edu (192.68.191.233)  1.879 ms  1.597 ms  1.654 ms
     7  sacrcr5-ip-a-sunncr5.es.net (134.55.40.5)  4.186 ms  4.281 ms  4.200 ms
     8  denvcr5-ip-a-sacrcr5.es.net (134.55.50.202)  25.249 ms  25.756 ms  25.174 ms
     9  kanscr5-ip-a-denvcr5.es.net (134.55.49.58)  35.657 ms  35.692 ms  35.681 ms
    10  chiccr5-ip-a-kanscr5.es.net (134.55.43.81)  46.718 ms  46.776 ms  46.813 ms
    11  washcr5-ip-a-chiccr5.es.net (134.55.36.46)  63.820 ms  63.948 ms  63.727 ms
    12  * * *
    13  198.124.216.98 (198.124.216.98)  69.492 ms  69.244 ms  69.746 ms
    14  dmz-rtr-1-ny32-rtr-1.mit.edu (18.192.5.1)  75.702 ms  75.761 ms  75.826 ms
    15  backbone-rtr-1-dmz-rtr-1.mit.edu (18.69.1.2)  75.583 ms  75.777 ms  75.730 ms
    16  oc11-rtr-1-backbone-rtr-1.mit.edu (18.123.69.2)  75.527 ms  75.665 ms  75.449 ms
    17  howe-and-ser-moving.mit.edu (18.9.64.27)  83.002 ms  83.282 ms  83.064 ms
    traceroute -m 30 -q 3 18.9.64.27 took 33secs. Total time=33secs. user=nobody
    ```

    Likewise, results from my Athena SSH to Stanford are below.

    ```bash
    traceroute to 134.79.197.200 (134.79.197.200), 30 hops max, 60 byte packets
     1  18.9.64.3 (18.9.64.3)  8.164 ms  8.195 ms  8.320 ms
     2  BACKBONE-RTR-1-OC11-RTR-1.MIT.EDU (18.123.69.1)  8.544 ms  8.367 ms  8.469 ms
     3  DMZ-RTR-1-BACKBONE-RTR-1.MIT.EDU (18.69.1.1)  9.251 ms  8.590 ms  9.299 ms
     4  NY32-RTR-1-DMZ-RTR-1.MIT.EDU (18.192.5.2)  15.366 ms  15.583 ms  15.440 ms
     5  aofasdn1-mit.es.net (198.124.216.97)  15.075 ms  15.486 ms  15.844 ms
     6  washcr5-ip-a-aofacr5.es.net (134.55.36.34)  20.018 ms  19.977 ms  19.759 ms
     7  chiccr5-ip-a-washcr5.es.net (134.55.36.45)  37.121 ms  37.330 ms  37.585 ms
     8  kanscr5-ip-a-chiccr5.es.net (134.55.43.82)  48.919 ms  48.402 ms  48.003 ms
     9  denvcr5-ip-a-kanscr5.es.net (134.55.49.57)  58.488 ms  58.889 ms  59.069 ms
    10  sacrcr5-ip-a-denvcr5.es.net (134.55.50.201)  79.838 ms  79.488 ms  80.172 ms
    11  sunncr5-ip-a-sacrcr5.es.net (134.55.40.6)  82.092 ms  82.385 ms  82.513 ms
    12  rtr-border2-p2p-sunn-cr5.slac.stanford.edu (192.68.191.234)  82.485 ms  82.876 ms  82.642 ms
    13  * * *
    14  * * *
    15  * * *
    16  * * *
    17  * * *
    18  * * *
    19  * * *
    20  * * *
    21  * * *
    22  * * *
    23  * * *
    24  * * *
    25  * * *
    26  * * *
    27  * * *
    28  * * *
    29  * * *
    30  * * *
    ```

7. Nothing seems unusual when comparing the output on both ends: our output indicates that the same routers are indeed traversed in both directions. In the case when this might not happen, there might have been a `time_exceeded` message sent from the destination, forcing the source to send its probing packets somewhere else to check (theoretically, as my commands worked fine).

# Blackholes
At the command prompt, type: `> traceroute 18.31.0.200`.

8. Show the output of the above command. Describe what is strange about the observed output, and why `traceroute` gives you such an output. Refer to `man traceroute` for useful hints.

*Write or type your answers to question 8 below. Your answers should not extend onto a second page.*

8. Output is as follows.

    ```bash
    traceroute to 18.31.0.200 (18.31.0.200), 30 hops max, 60 byte packets
     1  18.9.64.3 (18.9.64.3)  8.184 ms  8.458 ms  8.185 ms
     2  BACKBONE-RTR-1-OC11-RTR-1.MIT.EDU (18.123.69.1)  8.776 ms  9.024 ms  8.510 ms
     3  DMZ-RTR-1-BACKBONE-RTR-1.MIT.EDU (18.69.1.1)  8.549 ms  9.129 ms  9.055 ms
     4  DMZ-RTR-2-DMZ-RTR-1-1.MIT.EDU (18.192.2.2)  8.779 ms  8.941 ms  8.926 ms
     5  * * *
     6  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.502 ms  10.073 ms  10.069 ms
     7  * * *
     8  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  9.484 ms  9.954 ms  10.172 ms
     9  * * *
    10  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.200 ms  9.749 ms  11.729 ms
    11  * * *
    12  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.994 ms  10.901 ms  10.868 ms
    13  * * *
    14  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.002 ms  10.175 ms  8.776 ms
    15  * * *
    16  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.823 ms  10.319 ms  10.284 ms
    17  * * *
    18  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.775 ms  10.809 ms  11.084 ms
    19  * * *
    20  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  12.730 ms  12.713 ms  8.671 ms
    21  * * *
    22  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.200 ms  9.904 ms  8.952 ms
    23  * * *
    24  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  9.603 ms  9.124 ms  11.681 ms
    25  * * *
    26  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.466 ms  10.519 ms  10.318 ms
    27  * * *
    28  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.494 ms  10.541 ms  10.599 ms
    29  * * *
    30  DMZ-RTR-2-CSAIL.MIT.EDU (18.4.7.1)  10.861 ms  10.912 ms  10.969 ms
    ```

    This output is strange in the sense that packets are being lost (timeout limit of 5 seconds exceeded), but this is happening on every other line. I'm not quite sure of what this implies, but considering that the address `DMZ-RTR-2-CSAIL.MIT.EDU` is already reached and stayed on until 30 hops have been reached, it could be that the packets are stuck at this endpoint without anywhere to go to next, and thus die once reaching timeout.

# Border Gateway Protocol (BGP)
For this last question on the topic of Internet routing, you need to refer to the BGP routing table data below. This table shows all of the BGP routing entries that a particular router (near the University of Oregon) refers to when forwarding any packets to MIT (IP Address `18.*.*.*`).
As described in the Internet routing paper, recall that BGP is a path vector protocol. Each line of this table lists a distinct path from this router to MIT, from which it will choose one to use. The `Next Hop` field is the IP address of the router that forwards packets for each path listed in the table. The `Path` field is the list of autonomous systems the path traverses on its way to MIT. The other fields (`Metric`, `LocPrf`, `Weight`) may be used by the router to decide which one of the possible paths to use.

```
BGP table version is 9993576, local router ID is 198.32.162.100
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              S Stale
Origin codes: i - IGP, e - EGP, ? - incomplete
```

```
   Network          Next Hop            Metric LocPrf Weight Path
*  18.0.0.0         216.140.8.59           413             0 6395 3356 3 i
*                   216.140.2.59           982             0 6395 3356 3 i
*                   141.142.12.1                           0 1224 22335 11537 10578 3 i
*                   209.249.254.19         125             0 6461 3356 3 i
*                   202.232.0.2                            0 2497 3356 3 i
*                   209.10.12.125         8204             0 4513 3356 3 i
*                   208.51.113.253                         0 3549 174 16631 3 3 3 i
*                   209.123.12.51                          0 8001 1784 10578 3 i
*                   209.10.12.156            0             0 4513 3356 3 i
*                   195.66.224.82                          0 4513 3356 3 i
*                   209.10.12.28          8203             0 4513 3356 3 i
*                   203.181.248.233                        0 7660 11537 10578 3 i
*                   64.50.230.2                            0 4181 174 174 174 16631 3 3 3 i
*                   195.66.232.254                         0 5459 2649 174 174 174 16631 3 3 3 i
*                   195.66.232.239                         0 5459 2649 174 174 174 16631 3 3 3 i
*                   64.50.230.1                            0 4181 174 174 174 16631 3 3 3 i
*                   194.85.4.55                            0 3277 8482 29281 702 701 3356 3 i
*                   207.172.6.227           83             0 6079 10578 3 i
*                   207.172.6.162           62             0 6079 10578 3 i
*                   129.250.0.85            11             0 2914 174 16631 3 3 3 i
*                   206.220.240.95                         0 10764 11537 10578 3 i
*                   217.75.96.60                           0 16150 8434 3257 3356 3 i
*                   66.185.128.48          514             0 1668 3356 3 i
*                   206.24.210.26                          0 3561 3356 3 i
*                   216.191.65.118                         0 15290 174 16631 3 3 3 i
*                   216.191.65.126                         0 15290 174 16631 3 3 3 i
*                   209.161.175.4                          0 14608 19029 3356 3 i
*                   202.249.2.86                           0 7500 2497 3356 3 i
*                   208.186.154.35           0             0 5650 3356 3 i
*                   167.142.3.6                            0 5056 1239 3356 3 i
*                   64.200.151.12                          0 7911 3356 3 i
*                   195.219.96.239                         0 6453 3356 3 i
*                   208.186.154.36           0             0 5650 3356 3 i
*                   203.194.0.12                           0 9942 16631 174 174 174 16631 3 3 3 i
*                   213.200.87.254          40             0 3257 3356 3 i
*                   216.218.252.145                        0 6939 3356 3 i
*                   216.18.63.137                          0 6539 174 16631 3 3 3 i
*                   216.218.252.152                        0 6939 3356 3 i
*                   195.249.0.135                          0 3292 3356 3 i
*                   65.106.7.139             3             0 2828 174 16631 3 3 3 i
*                   207.45.223.244                         0 6453 3356 3 i
*                   207.246.129.14                         0 11608 6461 3356 3 i
*                   207.46.32.32                           0 8075 174 16631 3 3 3 i
*                   129.250.0.11             0             0 2914 174 16631 3 3 3 i
*                   134.55.200.1                           0 293 11537 10578 3 i
*                   193.0.0.56                             0 3333 3356 3 i
*                   216.140.14.186           3             0 6395 3356 3 i
*                   198.32.8.196           960             0 11537 10578 3 i
*                   64.200.95.239                          0 7911 3356 3 i
*                   196.7.106.245                          0 2905 701 3356 3 i
*                   154.11.63.86                           0 852 174 16631 3 3 3 i
*                   134.222.85.45            0             0 286 209 3356 3 i
*                   213.140.32.146                         0 12956 174 16631 3 3 3 i
*                   164.128.32.11                          0 3303 3356 3 i
*                   213.248.83.240                         0 1299 3356 3 i
*                   154.11.98.18                           0 852 174 16631 3 3 3 i
*                   4.68.0.243               0             0 3356 3 i
*                   204.42.253.253           0             0 267 2914 174 16631 3 3 3 i
*                   206.186.255.223                        0 2493 3602 174 16631 3 3 3 i
*                   193.251.128.22                         0 5511 3356 3 i
*                   203.62.252.26                          0 1221 4637 3356 3 i
*                   12.0.1.63                              0 7018 3356 3 i
*                   144.228.241.81  4294967294             0 1239 3356 3 i
```

9. From the path entry data, which Autonomous System (AS) number corresponds to MIT?
10. What are the Autonomous System (AS) numbers of each AS which advertises a direct link to MIT?
11. How long did it take you to complete this hands-on?

*Write or type your answers to questions 9-11 below on the next page. Your answer should not extend to an additional page.*
