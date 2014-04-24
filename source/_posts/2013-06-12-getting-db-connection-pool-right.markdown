---
layout: post
title: "Getting DB connection pool right"
date: 2013-06-12 22:23
comments: true
categories:  
---
Recently during performance testing, we found that our database connection pool size was way too high which resulted in bad performance of the system as we increased the load. This was due to not giving enough thought while increasing the connection pool a couple of months back when we faced a latency issue. So, the question was what should be the optimum value for connection pool size and how should we determine it. We were using Oracle as the database for our project.

To calculate the required connection pool size, we first established the most frequent request and the time  spent in the db on average in order to process that request. This turned out to be the creation/update of an order record.
On the basis of our usage model we expect 2500 users to be ordering concurrently and overall 300 orders to be requested per second. Looking at the server logs it takes around 250ms on average to fulfil an order request, out of which 100ms is spent in the db. So, for 300 requests to be processed within a second, we would need 30 connections on average. Similarly we identified the other most frequent requests and were able to calculate the average and maximum concurrent number of connections required. 

One of the things worth mentioning is that we are not using any database level cache in our system. If we were then we would have to take this into account when calculating the number of actual database hits.

The results with the new connection pool size were good - the latency in responding to requests was significantly reduced compared to when we had the very large connection pool. We also had a chat with the DBA who confirmed that the number of active connections was in the same ballpark as we had calculated. It would have been really useful for the development team to be able to monitor the number of active connections directly, but lack of access rights meant this wasn't possible. 

Getting the connection pool size right not only improved the request latency, but also addressed some other issues we were experiencing. Attempting to run with too many connections was leading to some serious paging issues with the buffer cache.

In conclusion, choosing the right connection pool size is an important task in performance tuning an application, and accepting the defaults can result in serious degradation.
 
It would be good to hear other people's experiences with setting connection pool sizes, what approaches have worked well and what to avoid.



  
