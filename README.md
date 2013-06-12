evilscan
===============

[![Build Status](https://secure.travis-ci.org/eviltik/evilscan.png)](http://travis-ci.org/eviltik/evilscan)

Massive ip/port scanner (tcp connect). Work in progress.

Status
-------
This program is young, the code is dirty and need a big refactoring. But well, it's working as expected at the moment.
Currently support :

* individual IP or IP range scan
* individual port, ports list, or ports range
* banner grabbing (not fully implemented, works with verbose ports only)
* IAC negotiation
* reverse dns
* geolocation information
* shell or json output
* optional progress details


Roadmap
--------
* code refactoring
* more and more tests
* add event emitters
* udp scan
* syn scan


Install
-------

>Travis-ci tests (using mocha+chai), tell us it's ok using **nodejs >= 0.9**. So you can install evilscan using npm, as usual :
>```
>npm install -g evilscan
>```


Command line options
-------
**--target=[ip|host|cidr range]**
>Specify the target you want to scan, examples :
>```
>--target=192.168.1.1
>--target=127.0.0.1/24 
>--target=test.com
>```

**--port=[single port|port-range|mixed]**
>Specific port(s) you want to scan, examples :
>```
>--port=80
>--port=21,23
>--port=21,23,5900-5910
>--port=0-65535
>```
If not specified, no scan. Usefull to fetch massive geoip infos or reverse dns.

**--reverse**
>Resolve DNS

**--banner**
>Grab the banner, convert it into ascii, encode special chars (\r\n\t), limited to 512 bytes at the moment. 

**--raw**
>To be documented

**--isclose [=true|false, default false]**
>Shortcut for --isrefused and --istimeout

**--isrefuse [=true|false, default false]**
>Show only ports having connection refused

**--istimeout [=true|false, default false]**
>Show only ports having connection timeout

**--isopen[=true|false, default true]**
>Show opened ports (default)
>Remove opened port from the result if value is false

**--geo**
>Add geoip information (city,country,latitude,longitude)

**--json**
>Show result as a json string

**--progress**
>Show progress status every seconds


Example usage
----------------

* Every ports on localhost, grab banner, display only opened ports 
```
root@debian:~# evilscan --target=127.0.0.1 --port=0-65535 --banner
127.0.0.1:81|open
127.0.0.1:53|open
127.0.0.1:111|open
127.0.0.1:23|open|Debian GNU/Linux jessie/sid\r\ndebian login:
127.0.0.1:5432|open
127.0.0.1:27017|open
127.0.0.1:28017|open
127.0.0.1:33746|open
127.0.0.1:35854|open
127.0.0.1:40076|open
127.0.0.1:40802|open
127.0.0.1:55553|open
127.0.0.1:57523|open
```

* Every ports on localhost, grab banner, display only opened ports, json output, progress status each seconds
```
root@debian:~# evilscan --target=127.0.0.1 --port=0-65535 --banner --isopen --istimeout --progress --json
"_timeStart":"N/A","_timeElapsed":"N/A","_jobsTotal":65535,"_jobsRunning":0,"_jobsDone":0,"_progress":0,"_concurrency":800,"_status":"Starting","_message":"Starting"}
{"_timeStart":1370867204668,"_timeElapsed":1061,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":6950,"_progress":10,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:6211"}
{"ip":"127.0.0.1","port":111,"status":"open"}
{"ip":"127.0.0.1","port":81,"status":"open"}
{"ip":"127.0.0.1","port":53,"status":"open"}
{"ip":"127.0.0.1","port":23,"status":"open","banner":"Debian GNU/Linux jessie/sid\\r\\ndebian login:"}
{"ip":"127.0.0.1","port":5432,"status":"open"}
{"_timeStart":1370867204668,"_timeElapsed":2104,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":14761,"_progress":22,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:13965"}
{"_timeStart":1370867204668,"_timeElapsed":3220,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":23187,"_progress":35,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:22395"}
{"_timeStart":1370867204668,"_timeElapsed":4223,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":30874,"_progress":47,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:30110"}
{"ip":"127.0.0.1","port":27017,"status":"open"}
{"ip":"127.0.0.1","port":28017,"status":"open"}
{"_timeStart":1370867204668,"_timeElapsed":5223,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":38657,"_progress":58,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:37878"}
{"ip":"127.0.0.1","port":33953,"status":"open"}
{"ip":"127.0.0.1","port":36712,"status":"open"}
{"_timeStart":1370867204668,"_timeElapsed":6271,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":46369,"_progress":70,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:45578"}
{"ip":"127.0.0.1","port":40802,"status":"open"}
{"_timeStart":1370867204668,"_timeElapsed":7365,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":54829,"_progress":83,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:54108"}
{"ip":"127.0.0.1","port":53013,"status":"open"}
{"ip":"127.0.0.1","port":53656,"status":"open"}
{"ip":"127.0.0.1","port":53632,"status":"open"}
{"ip":"127.0.0.1","port":53400,"status":"open"}
{"_timeStart":1370867204668,"_timeElapsed":8394,"_jobsTotal":65535,"_jobsRunning":800,"_jobsDone":62449,"_progress":95,"_concurrency":800,"_status":"Running","_message":"Scanned 127.0.0.1:61697"}
{"_timeStart":1370867204668,"_timeElapsed":8744,"_jobsTotal":65535,"_jobsRunning":0,"_jobsDone":65535,"_progress":100,"_concurrency":800,"_status":"Finished","_message":"Scanned 127.0.0.1:64739"}
```

Tips :
=======
**Conncurrency and fast scan**

By default, concurrency is 100. Which is slow when you are scanning large ip range or large port range. You can pass a bigger value using --concurrency option. 1000 is fast by example.
On some linux, only 1024 opened sockets are allowed in the same time. To break this limit, you have to update ulimit parameter of your linux first :

```
ulimit -u unlimited
```

**Pause/unpause**

You can pause/unpause a running scan by sending SIGUSR1 signal. First time it will pause the process, second time it will unpause it.
```
kill -SIGUSR1 19859 # where 19859 is the pid of nodejs process running evilscan
```

Help needed
=======

Do not hesitate to open issues and ask for features. Right now i just need "connect" scan. Forks are welcome. UDP implementation or syn scans should be great. Code refactoring too, and more tests.

If you are using evilscan, **please star it** !



Thank you
=======
Special thanks to authors of theses nodejs modules :
* [node-portscanner](https://github.com/baalexander/node-portscanner) for the inspiration
* [qjobs](https://github.com/franck34/qjobs "qjobs") pause/unpause queue jobs is really cool

and [dailyjs](http://dailyjs.com/) ;)


