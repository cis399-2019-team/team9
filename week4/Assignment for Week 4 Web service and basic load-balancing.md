# Assignment for Week 4: Web service and basic load-balancing

## Put up a web page on your system in its document root. Send me the URLs to that page (`http://ip-x-x-x-x.aws.amazon.com/foo.html` and I'll try to retrieve it.

```
http://ec2-54-201-34-210.us-west-2.compute.amazonaws.com/
```

## In addition, find log messages showing accesses to a web page in your web server document root, and send me samples of those log messages.

```bash
ubuntu@ip-10-0-6-163:~$ tail -f /var/log/nginx/access.log
128.223.222.50 - - [17/Jul/2019:00:26:32 +0000] "GET / HTTP/1.1" 200 293 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
128.223.222.50 - - [17/Jul/2019:00:26:32 +0000] "GET /index.css HTTP/1.1" 200 125 "http://ec2-54-201-34-210.us-west-2.compute.amazonaws.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
128.223.222.50 - - [17/Jul/2019:00:29:29 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
128.223.222.50 - - [17/Jul/2019:00:29:29 +0000] "GET /index.css HTTP/1.1" 304 0 "http://ec2-54-201-34-210.us-west-2.compute.amazonaws.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
```

## Provide the domain name associated with your load balancer so I can test web server access to it.



## Describe the results of testing your load balancer when you shut down one, then both, back-end instances, and when you restore them.

## Provide excerpts from web server logs from each of your web server instances showing load balancer health checks and accesses to your web content.

## Indicate the Puppet code you use to replicate content between your web server instances.