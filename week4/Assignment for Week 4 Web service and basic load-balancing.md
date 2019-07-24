# Assignment for Week 4: Web service and basic load-balancing

[TOC]



## Put up a web page on your system in its document root. Send me the URLs to that page (`http://ip-x-x-x-x.aws.amazon.com/foo.html` and I'll try to retrieve it.

Currently it is:

```
http://ec2-34-210-33-233.us-west-2.compute.amazonaws.com/index.html
or
http://ec2-54-191-194-121.us-west-2.compute.amazonaws.com/index.html
```

## In addition, find log messages showing accesses to a web page in your web server document root, and send me samples of those log messages.

```bash
128.223.222.50 - - [24/Jul/2019:22:39:16 +0000] "GET / HTTP/1.1" 200 293 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
128.223.222.50 - - [24/Jul/2019:22:39:16 +0000] "GET /index.css HTTP/1.1" 200 125 "http://ec2-34-210-33-233.us-west-2.compute.amazonaws.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
128.223.222.50 - - [24/Jul/2019:22:39:17 +0000] "GET /favicon.ico HTTP/1.1" 404 209 "http://ec2-34-210-33-233.us-west-2.compute.amazonaws.com/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36"
```

## Provide the domain name associated with your load balancer so I can test web server access to it.

```
http://JerryNewLB-1481786165.us-west-2.elb.amazonaws.com
```

## Describe the results of testing your load balancer when you shut down one, then both, back-end instances, and when you restore them.

### After shutting down one instance:

- In the Instances tab of Load Balancer page on AWS console, the instance I turned off has a status "OutOfService."
- Checking the access log of the only instance left, when accessing the domain name associated with the load balancer, I could see my access record.

### After shutting down both:

- In the Instances tab of Load Balancer page on AWS console, both instances have a status "OutOfService."
- When accessing the domain name associated with the load balancer, a 503 error is returned.

### After restoring them one by one:

- Able to access load balancer's domain name, though both of two instances public domain names have changed.

## Provide excerpts from web server logs from each of your web server instances showing load balancer health checks and accesses to your web content.

```bash
root@ip-10-0-9-29:/etc/puppet# tail -f /var/log/nginx/access.log
10.0.9.235 - - [24/Jul/2019:22:39:28 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:39:34 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:39:58 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:40:04 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:40:28 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:40:34 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
```

```bash
ubuntu@ip-10-0-9-123:~$ tail -f /var/log/nginx/access.log
10.0.9.129 - - [24/Jul/2019:22:40:34 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:40:58 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:41:04 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:41:28 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:41:34 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:41:58 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:42:04 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.235 - - [24/Jul/2019:22:42:28 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
10.0.9.129 - - [24/Jul/2019:22:42:34 +0000] "GET /index.html HTTP/1.1" 200 388 "-" "ELB-HealthChecker/1.0"
```

## Indicate the Puppet code you use to replicate content between your web server instances.

In my repository, the module "pageserver" was used to deploy the web page server.

```java
class pageserver {
	contain pageserver::make_sure_package_and_service_are_good
	contain pageserver::html_resources_to_be_imported

	# import html resources after packages are installed
	# and services are running
	Class['pageserver::make_sure_package_and_service_are_good']
	-> Class['pageserver::html_resources_to_be_imported']
}
class pageserver::make_sure_package_and_service_are_good {
    package { 'nginx':
      ensure => 'present',
      before => [
            File['/etc/nginx/sites-available/default'],
      ],
    }
    service { 'nginx':
      ensure => 'running',
      enable => true,
      require => [
        Package['nginx'],
      ],
      subscribe => [
        File['/etc/nginx/sites-available/default'],
      ],
    }
    file { '/etc/nginx/sites-available/default':
        ensure => file,
        notify  => Service['nginx'],
        source => 'puppet:///modules/pageserver/nginx_site_config',
        mode    => "0664",
        owner   => root,
        group   => root,
    }
}
class pageserver::html_resources_to_be_imported {
	file { '/var/www/html/':
        ensure => directory,
        mode    => "0755",
        owner   => root,
        group   => root,
        before => [
            File['/var/www/html/index.html'],
            File['/var/www/html/index.css'],
      ],
    }
    file { '/var/www/html/index.html':
        ensure => file,
        source => 'puppet:///modules/pageserver/index.html',
        mode    => "0664",
        owner   => root,
        group   => root,
    }
    file { '/var/www/html/index.css':
        ensure => file,
        source => 'puppet:///modules/pageserver/index.css',
        mode    => "0664",
        owner   => root,
        group   => root,
    }
}
```

