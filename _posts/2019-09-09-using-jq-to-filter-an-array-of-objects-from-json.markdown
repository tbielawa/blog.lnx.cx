---
author: tbielawa
comments: true
date: 2019-09-09 17:18:50+00:00
layout: post
link: https://blog.lnx.cx/2019/09/09/using-jq-to-filter-an-array-of-objects-from-json/
slug: using-jq-to-filter-an-array-of-objects-from-json
title: Using jq to filter an array of objects from JSON
wordpress_id: 1026
categories:
- /dev/null
- GNU/Linux
- Programming
tags:
- amazon
- array
- filter
- jq
- json
- list
- map
---

For some reason it took me an unreasonable amount of time to figure out how to filter an array (or list) of objects from a JSON stream. Every single example I found was a little too weird for me, or resulted in printing each object, but not in a final array format. Here's what I came up with:




Say for example you are parsing the [AWS IP ranges JSON stream](https://aws.amazon.com/blogs/aws/aws-ip-ranges-json/), you will receive an object like this:



    
    {
      "syncToken": "1567728788",
      "createDate": "2019-09-06-00-13-08",
      "prefixes": [
        {
          "ip_prefix": "18.208.0.0/13",
          "region": "us-east-1",
          "service": "AMAZON"
        },
        ... more objects here ...




I was attempting to filter this down to ONLY objects where the `service` attribute was `AMAZON`. Using this jql I would get objects printed one after the other which is not what I wanted:



    
    $ jq -c '.prefixes[] | select(.service=="AMAZON")' < ip-ranges.json | head<br></br>{"ip_prefix":"18.208.0.0/13","region":"us-east-1","service":"AMAZON"}<br></br>{"ip_prefix":"52.95.245.0/24","region":"us-east-1","service":"AMAZON"}<br></br>{"ip_prefix":"99.77.142.0/24","region":"ap-east-1","service":"AMAZON"}




The correct syntax was ultimately very similar. 



    
    $ jq '.prefixes | map(. | select(.service=="AMAZON"))' < ip-ranges.json  | head<br></br>[<br></br>  {<br></br>    "ip_prefix": "18.208.0.0/13",<br></br>    "region": "us-east-1",<br></br>    "service": "AMAZON"<br></br>  },<br></br><br></br>




Now we are getting each object returned as a member of an array. The difference is that we're putting the `.prefixes` array objects into the `map` function and telling it to iterate every object through the `select` function. The `map` takes all of those matching objects and returns them as an array, whereas, previously we were only selecting objects that matched our `select` criteria. To get the objects back in a list we required the `map`. 







