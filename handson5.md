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

To figure out your machine's IP address, run `/sbin/ifconfig`. You'll get a lot of information, including its IP:
