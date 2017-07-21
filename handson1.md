# Exercises for Hands-on 1: DNS
You should submit answers only to the questions asked. In particular, please do not include pages of output from `dig` unless specifically requested.

# Getting Started
1. Using `dig`, find the IP address for `thyme.lcs.mit.edu`. What is the IP address?
2. The `dig` answer for `thyme` includes a record of type `CNAME`. In the terminology of chapter 4, what does `CNAME` mean?
3. What is the expiration time for the `thyme` `CNAME` record?
4. Run these commands to find what the computer you're using gets when it looks up `“data”` and `“data.”`. What are the two resulting IP addresses?
```
dig +domain=mit.edu data
dig +domain=mit.edu data.
```

5. Why are the results different? Look at the man page for `dig` to see what the `+domain=`
parameter does. Based on the output of the two commands, what is the difference between the DNS searches being performed for `data` and `data.`?

*Write or type your answers to questions 1-5 below. Your answers should not extend onto a second page.*

1. The IP address of `thyme.lcs.mit.edu` is `18.26.0.122`.
2. `CNAME` stands for “canonical name”, or, as the textbook would have it, an alias or indirect reference.
3. The expiration time is 1800 seconds, or 30 minutes.
4. For `data`, the IP address is `18.17.196.100`. For `data.`, the IP address is `65.22.96.25`.
5. The results are different because `data` is a relative path, whereas `data.` is an absolute path that will not check subdomains under the hierarchy.

# Understanding Hierarchy
For this problem, you will go through the steps of resolving a particular hostname, mimicking a standard recursive query. Assuming it knows nothing else about a name, a DNS resolver will ask a well-known root server. The root servers on the Internet are in the domain `root-servers.net`. One way to get a list of them is with the command:
```
athena% dig . ns
```

6. Use `dig` to ask one of the root servers the address of `lirone.csail.mit.edu`, without recursion. What command do you use to do this?
7. It is unlikely that these servers actually know the answer so they will refer you to a server (or list of servers) that might know more. Go through the hierarchy from the root without recursion, following the referrals manually, until you have found the address of `lirone.csail.mit.edu`. What commands did you use to do this? What IP address did you find for `lirone`?

*Write or type your answers to questions 6-7 below. Your answers should not extend onto a second page.*

6. We use dig `@servername targetname +norecurs` to do this.
7. I used the following sequence of commands:
```
dig @m.root-servers.net lirone.csail.mit.edu +norecurs
dig @a.edu-servers.net lirone.csail.mit.edu +norecurs
dig @usw2.akam.net lirone.csail.mit.edu +norecurs
dig @auth-ns2.csail.mit.edu lirone.csail.mit.edu +norecurs
```

This sequence of commands led to an IP address of `128.52.129.186` for `lirone`.

# Understanding Caching
These queries will show you how your local machine's DNS cache works.

8. Ask your default server for information, without recursion, about the host `www.dmoz.org`. What command did you use? Did your default server have the answer in its cache? How do you know? How long did this query take? If this information was cached, please find some other host name that is not cached and do this section with that other host.
9. Now, ask your default server this same query but with recursion. It should return an answer for you. How long did this take?
10. Finally, ask your default server again without recursion. How long does this request take? Has the cache served its purpose?

*Write or type your answers to questions 8-10 below (note that question 8 has three parts). Your answers should not extend onto a second page.*

8. I used `dig www.dmoz.org +norecurs`. The default server didn’t have the answer. There is no `ANSWER` section. The query time was 4ms.
9. This query with recursion took 312ms.
10. This request took 6ms and gave the answer we were looking for. The cache worked.
