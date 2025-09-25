---
title: "Bsides Foreniscs"
date: 2025-09-24T19:30:06-04:00
draft: true
---

In simple terms, DNS tunneling is a type of attack where hackers embed malicious code into a message that appears to be a normal request. 

DNS is a protocol that provides conversations between domain names and IP addresses. 

To visit a website, you need to know the IP address of the server hosting the website. 

To understand DNS simply, think of when a UMass student needs to go to `canvas.umass.edu`. the DNS server needs to ask a `.com` DNS server for the IP address of the `canvas.umass.edu` DNS server. A second request would provide the I address of the server hosting the desired webpage. 

DNS tunneling leverage this characteristic of the DNS traffic to implement a commmand and control channel. 



Let's take a look of a DNS request captured with Wireshark: `Standard query 0x7018 A xn--part3-hw3b.xgk3djnzts2mr.bsidesct.xn--org-9o0a OPT`
So Almost anything can be a domain name.

For the sake of simplicity, just know `A` is the record type of this DNS query. `A` helps maps the domain name to an IPv4 address. 

