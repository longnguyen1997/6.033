# Exercises for Hands-on 1: DNS
You should submit answers only to the questions asked. In particular, please do not include pages of output from `dig` unless specifically requested.

# Getting Started
1. Using `dig`, find the IP address for `thyme.lcs.mit.edu`. What is the IP address?
2. The `dig` answer for `thyme` includes a record of type `CNAME`. In the terminology of chapter 4, what does `CNAME` mean?
3. What is the expiration time for the `thyme` `CNAME` record?
4. Run these commands to find what the computer you're using gets when it looks up `“data”` and `“data.”`.
```
dig +domain=mit.edu data
dig +domain=mit.edu data.
```
What are the two resulting IP addresses?
5. Why are the results different? Look at the man page for `dig` to see what the `+domain=`
parameter does. Based on the output of the two commands, what is the difference between the DNS searches being performed for `data` and `data.`?

*Write or type your answers to questions 1-5 below. Your answers should not extend onto a second page.*

1. The IP address of `thyme.lcs.mit.edu` is `18.26.0.122`.
2. `CNAME` stands for “canonical name”, or, as the textbook would have it, an alias or indirect reference.
3. The expiration time is 1800 seconds, or 30 minutes.
4. For `“data”`, the IP address is `18.17.196.100`. For `“data.”`, the IP address is `65.22.96.25`.
