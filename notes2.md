Monit	install, understand, figure out everything it currently does in Taperoll and Ansiblestart, stop, restart nginx
	how monit interacts with Redis
	grub(?)swap file and fstab
	
	
	see what user it runs under in production
	docs say to run from dedicated user account, but Taperole does it with sudo
	
	deployer@bwelstudy:~$ cat /etc/passwd
	root:x:0:0:root:/root:/bin/bash
	daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
	bin:x:2:2:bin:/bin:/usr/sbin/nologin
	sys:x:3:3:sys:/dev:/usr/sbin/nologin
	sync:x:4:65534:sync:/bin:/bin/sync
	games:x:5:60:games:/usr/games:/usr/sbin/nologin
	man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
	lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
	mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
	news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
	uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
	proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
	www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
	backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
	list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
	irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
	gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
	nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
	libuuid:x:100:101::/var/lib/libuuid:
	syslog:x:101:104::/home/syslog:/bin/false
	messagebus:x:102:106::/var/run/dbus:/bin/false
	landscape:x:103:109::/var/lib/landscape:/bin/false
	sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
	pollinate:x:105:1::/var/cache/pollinate:/bin/false
	ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
	bwel:x:1001:1001:bwel:/home/bwel:/bin/bash
	deployer:x:1002:100::/home/deployer:/bin/bash
	postgres:x:106:113:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
	memcache:x:107:114:Memcached,,,:/nonexistent:/bin/false
	redis:x:108:115:redis server,,,:/var/lib/redis:/bin/false
	jrf:x:1003:1003:,,,:/home/jrf:/bin/bash
	deployer@bwelstudy:~$ 
	
sudo monit status	deployer@bwelstudy:~$ sudo monit status
	The Monit daemon 5.6 uptime: 71d 0h 55m 
	
	Process 'websocket_rails'
	  status                            Running
	  monitoring status                 Monitored
	  pid                               8619
	  parent pid                        1
	  uptime                            3h 10m 
	  children                          0
	  memory kilobytes                  177288
	  memory kilobytes total            177288
	  memory percent                    1.0%
	  memory percent total              1.0%
	  cpu percent                       0.0%
	  cpu percent total                 0.0%
	  data collected                    Tue, 11 Feb 2020 15:49:29
	
	Process 'unicorn'
	  status                            Running
	  monitoring status                 Monitored
	  pid                               7366
	  parent pid                        1
	  uptime                            3h 10m 
	  children                          8
	  memory kilobytes                  16608
	  memory kilobytes total            5352232
	  memory percent                    0.1%
	  memory percent total              32.5%
	  cpu percent                       0.0%
	  cpu percent total                 0.3%
	  data collected                    Tue, 11 Feb 2020 15:49:29
	
	Process 'redis-server'
	  status                            Running
	  monitoring status                 Monitored
	  pid                               1084
	  parent pid                        1
	  uptime                            71d 0h 55m 
	  children                          0
	  memory kilobytes                  9048
	  memory kilobytes total            9048
	  memory percent                    0.0%
	  memory percent total              0.0%
	  cpu percent                       0.0%
	  cpu percent total                 0.0%
	  port response time                0.000s to 127.0.0.1:6379 [DEFAULT via TCP]
	  data collected                    Tue, 11 Feb 2020 15:49:29
	
	Process 'nginx'
	  status                            Running
	  monitoring status                 Monitored
	  pid                               28612
	  parent pid                        1
	  uptime                            12d 13h 52m 
	  children                          4
	  memory kilobytes                  1504
	  memory kilobytes total            22388
	  memory percent                    0.0%
	  memory percent total              0.1%
	  cpu percent                       0.0%
	  cpu percent total                 0.0%
	  data collected                    Tue, 11 Feb 2020 15:49:29
	
	Process 'memcached'
	  status                            Running
	  monitoring status                 Monitored
	  pid                               1184
	  parent pid                        1
	  uptime                            71d 0h 55m 
	  children                          0
	  memory kilobytes                  1192
	  memory kilobytes total            1192
	  memory percent                    0.0%
	  memory percent total              0.0%
	  cpu percent                       0.0%
	  cpu percent total                 0.0%
	  port response time                0.000s to 127.0.0.1:11211 [DEFAULT via TCP]
	  data collected                    Tue, 11 Feb 2020 15:49:29
	
	System 'bwelstudy'
	  status                            Running
	  monitoring status                 Monitored
	  load average                      [0.07] [0.04] [0.05]
	  cpu                               0.5%us 0.0%sy 0.0%wa
	  memory usage                      6140720 kB [37.3%]
	  swap usage                        920 kB [0.0%]
	  data collected                    Tue, 11 Feb 2020 15:49:29
configure monit	https://mmonit.com/wiki/Monit/Systemd
	
	1. Install Monit as root:
	  ./configure
  make
  make install
	2. Add monit service configuration to systemd:
	/lib/systemd/system/monit.service
	 # This file is systemd template for monit service. To
 # register monit with systemd, place the monit.service file
 # to the /lib/systemd/system/ directory and then start it
 # using systemctl (see below).
 #
 # Enable monit to start on boot: 
 #         systemctl enable monit.service
 #
 # Start monit immediately: 
 #         systemctl start monit.service
 #
 # Stop monit:
 #         systemctl stop monit.service
 #
 # Status:
 #         systemctl status monit.service
	[Unit]
 Description=Pro-active monitoring utility for unix systems
 After=network.target
 Documentation=man:monit(1) https://mmonit.com/wiki/Monit/HowTo 
	[Service]
 Type=simple
 KillMode=process
 ExecStart=/usr/local/bin/monit -I
 ExecStop=/usr/local/bin/monit quit
 ExecReload=/usr/local/bin/monit reload
 Restart = on-abnormal
 StandardOutput=null
	[Install]
 WantedBy=multi-user.target
	3. Enable and start monit:
	  systemctl enable monit.service
  systemctl start monit.service
	
	
	
deploy@ubuntu18hb:~$ sudo systemctl enable monit	deploy@ubuntu18hb:~$ sudo systemctl enable monit
	[sudo] password for deploy: 
	monit.service is not a native service, redirecting to systemd-sysv-install.
	Executing: /lib/systemd/systemd-sysv-install enable monit
Monit: the monit HTTP interface is not enabled, please add the 'set httpd' statement and use the 'allow' option to allow monit to connect	https://askubuntu.com/questions/1097877/how-do-i-set-http-in-monitYou need to configure monit web interface in file monitrc.
	sudo nano /etc/monit/monitrc
	There is already configuration for emabling web interface but it is commented bydefault. You can comment out following lines already present in monitrc file. You can change bydefault username and password for security reasons.
	set httpd port 2812
     allow admin:monit      # require user 'admin' with password 'monit'
	Reload monit configuration after editing configuration file.
	sudo monit reload
monitrc file	https://websiteforstudents.com/install-monit-system-monitor-on-ubuntu-18-04-16-04-lts/
monit admin via ssl	https://mmonit.com/wiki/Monit/EnableSSLInMonit
	Basic configuration
	To enable SSL in Monit's HTTP GUI, add the SSL option to the SET HTTPD statement and specify the location of the PEM encoded server certificate using the PEMFILE option:
	 SET HTTPD PORT 2812
     WITH SSL {
         PEMFILE:  /etc/ssl/certs/monit.pem
     }
     ALLOW myuser:mypassword
	The PEMFILE should contain the server's private key and certificate. See Generating a self-signed certificate for testing if you don't have a CA signed certificate yet.
	Start Monit and connect to the Monit HTTP GUI with SSL via this url:
	 https://localhost:2812/
	
monit docs	https://mmonit.com/monit/#documentation
monit configuration examples	https://mmonit.com/wiki/Monit/ConfigurationExamples
	
	NGINX
	check process nginx with pidfile /var/run/nginx.pid
  start program = "/etc/init.d/nginx start"
  stop program  = "/etc/init.d/nginx stop"
  group www-data (for ubuntu, debian)
	
	PostgreSQL
	Generally choosing either the socket or a TCP/IP connect is sufficient.
	 check process postgres with pidfile /var/postgres/postmaster.pid
   group database
   start program = "/etc/init.d/postgresql start"
   stop  program = "/etc/init.d/postgresql stop"
   if failed unixsocket /var/run/postgresql/.s.PGSQL.5432 protocol pgsql 
      then restart
   if failed host 192.168.1.1 port 5432 protocol pgsql then restart
	
	
prod example	deployer@bwelstudy:~$ sudo cat /etc/monit/monitrc
	###############################################################################
	## Monit control file
	###############################################################################
	##
	## Comments begin with a '#' and extend through the end of the line. Keywords
	## are case insensitive. All path's MUST BE FULLY QUALIFIED, starting with '/'.
	##
	## Below you will find examples of some frequently used statements. For 
	## information about the control file and a complete list of statements and 
	## options, please have a look in the Monit manual.
	##
	##
	###############################################################################
	## Global section
	###############################################################################
	##
	## Start Monit in the background (run as a daemon):
	#
	  set daemon 120            # check services at 2-minute intervals
	#   with start delay 240    # optional: delay the first check by 4-minutes (by 
	#                           # default Monit check immediately after Monit start)
	#
	#
	## Set syslog logging with the 'daemon' facility. If the FACILITY option is
	## omitted, Monit will use 'user' facility by default. If you want to log to 
	## a standalone log file instead, specify the full path to the log file
	#
	# set logfile syslog facility log_daemon                       
	  set logfile /var/log/monit.log
	#
	#
	## Set the location of the Monit id file which stores the unique id for the
	## Monit instance. The id is generated and stored on first Monit start. By 
	## default the file is placed in $HOME/.monit.id.
	#
	# set idfile /var/.monit.id
	  set idfile /var/lib/monit/id
	#
	## Set the location of the Monit state file which saves monitoring states
	## on each cycle. By default the file is placed in $HOME/.monit.state. If
	## the state file is stored on a persistent filesystem, Monit will recover
	## the monitoring state across reboots. If it is on temporary filesystem, the
	## state will be lost on reboot which may be convenient in some situations.
	#
	  set statefile /var/lib/monit/state
	#
	## Set the list of mail servers for alert delivery. Multiple servers may be 
	## specified using a comma separator. If the first mail server fails, Monit 
	# will use the second mail server in the list and so on. By default Monit uses 
	# port 25 - it is possible to override this with the PORT option.
	#
	# set mailserver mail.bar.baz,               # primary mailserver
	#                backup.bar.baz port 10025,  # backup mailserver on port 10025
	#                localhost                   # fallback relay
	#
	#
	## By default Monit will drop alert events if no mail servers are available. 
	## If you want to keep the alerts for later delivery retry, you can use the 
	## EVENTQUEUE statement. The base directory where undelivered alerts will be 
	## stored is specified by the BASEDIR option. You can limit the maximal queue
	## size using the SLOTS option (if omitted, the queue is limited by space 
	## available in the back end filesystem).
	#
	  set eventqueue
	      basedir /var/lib/monit/events # set the base directory where events will be stored
	      slots 100                     # optionally limit the queue size
	#
	#
	## Send status and events to M/Monit (for more informations about M/Monit 
	## see http://mmonit.com/). By default Monit registers credentials with 
	## M/Monit so M/Monit can smoothly communicate back to Monit and you don't
	## have to register Monit credentials manually in M/Monit. It is possible to
	## disable credential registration using the commented out option below. 
	## Though, if safety is a concern we recommend instead using https when
	## communicating with M/Monit and send credentials encrypted.
	#
	# set mmonit http://monit:monit@192.168.1.10:8080/collector
	#     # and register without credentials     # Don't register credentials
	#
	#
	## Monit by default uses the following format for alerts if the the mail-format
	## statement is missing::
	## --8<--
	## set mail-format {
	##      from: monit@$HOST
	##   subject: monit alert --  $EVENT $SERVICE
	##   message: $EVENT Service $SERVICE
	##                 Date:        $DATE
	##                 Action:      $ACTION
	##                 Host:        $HOST
	##                 Description: $DESCRIPTION
	##
	##            Your faithful employee,
	##            Monit
	## }
	## --8<--
	##
	## You can override this message format or parts of it, such as subject
	## or sender using the MAIL-FORMAT statement. Macros such as $DATE, etc.
	## are expanded at runtime. For example, to override the sender, use:
	#
	# set mail-format { from: monit@foo.bar }
	#
	#
	## You can set alert recipients whom will receive alerts if/when a 
	## service defined in this file has errors. Alerts may be restricted on 
	## events by using a filter as in the second example below. 
	#
	# set alert sysadm@foo.bar                       # receive all alerts
	## Do not alert when Monit start,stop or perform a user initiated action
	# set alert manager@foo.bar not on { instance, action } 
	#
	#
	## Monit has an embedded web server which can be used to view status of 
	## services monitored and manage services from a web interface. See the
	## Monit Wiki if you want to enable SSL for the web server. 
	#
	# set httpd port 2812 and
	#    use address localhost  # only accept connection from localhost
	#    allow localhost        # allow localhost to connect to the server and
	#    allow admin:monit      # require user 'admin' with password 'monit'
	#    allow @monit           # allow users of group 'monit' to connect (rw)
	#    allow @users readonly  # allow users of group 'users' to connect readonly
	#
	###############################################################################
	## Services
	###############################################################################
	##
	## Check general system resources such as load average, cpu and memory
	## usage. Each test specifies a resource, conditions and the action to be
	## performed should a test fail.
	#
	#  check system myhost.mydomain.tld
	#    if loadavg (1min) > 4 then alert
	#    if loadavg (5min) > 2 then alert
	#    if memory usage > 75% then alert
	#    if swap usage > 25% then alert
	#    if cpu usage (user) > 70% then alert
	#    if cpu usage (system) > 30% then alert
	#    if cpu usage (wait) > 20% then alert
	#
	#    
	## Check if a file exists, checksum, permissions, uid and gid. In addition
	## to alert recipients in the global section, customized alert can be sent to 
	## additional recipients by specifying a local alert handler. The service may 
	## be grouped using the GROUP option. More than one group can be specified by
	## repeating the 'group name' statement.
	#    
	#  check file apache_bin with path /usr/local/apache/bin/httpd
	#    if failed checksum and 
	#       expect the sum 8f7f419955cefa0b33a2ba316cba3659 then unmonitor
	#    if failed permission 755 then unmonitor
	#    if failed uid root then unmonitor
	#    if failed gid root then unmonitor
	#    alert security@foo.bar on {
	#           checksum, permission, uid, gid, unmonitor
	#        } with the mail-format { subject: Alarm! }
	#    group server
	#
	#    
	## Check that a process is running, in this case Apache, and that it respond
	## to HTTP and HTTPS requests. Check its resource usage such as cpu and memory,
	## and number of children. If the process is not running, Monit will restart 
	## it by default. In case the service is restarted very often and the 
	## problem remains, it is possible to disable monitoring using the TIMEOUT
	## statement. This service depends on another service (apache_bin) which
	## is defined above.
	#    
	#  check process apache with pidfile /usr/local/apache/logs/httpd.pid
	#    start program = "/etc/init.d/httpd start" with timeout 60 seconds
	#    stop program  = "/etc/init.d/httpd stop"
	#    if cpu > 60% for 2 cycles then alert
	#    if cpu > 80% for 5 cycles then restart
	#    if totalmem > 200.0 MB for 5 cycles then restart
	#    if children > 250 then restart
	#    if loadavg(5min) greater than 10 for 8 cycles then stop
	#    if failed host www.tildeslash.com port 80 protocol http 
	#       and request "/somefile.html"
	#       then restart
	#    if failed port 443 type tcpssl protocol http
	#       with timeout 15 seconds
	#       then restart
	#    if 3 restarts within 5 cycles then timeout
	#    depends on apache_bin
	#    group server
	#    
	#    
	## Check filesystem permissions, uid, gid, space and inode usage. Other services,
	## such as databases, may depend on this resource and an automatically graceful
	## stop may be cascaded to them before the filesystem will become full and data
	## lost.
	#
	#  check filesystem datafs with path /dev/sdb1
	#    start program  = "/bin/mount /data"
	#    stop program  = "/bin/umount /data"
	#    if failed permission 660 then unmonitor
	#    if failed uid root then unmonitor
	#    if failed gid disk then unmonitor
	#    if space usage > 80% for 5 times within 15 cycles then alert
	#    if space usage > 99% then stop
	#    if inode usage > 30000 then alert
	#    if inode usage > 99% then stop
	#    group server
	#
	#
	## Check a file's timestamp. In this example, we test if a file is older 
	## than 15 minutes and assume something is wrong if its not updated. Also,
	## if the file size exceed a given limit, execute a script
	#
	#  check file database with path /data/mydatabase.db
	#    if failed permission 700 then alert
	#    if failed uid data then alert
	#    if failed gid data then alert
	#    if timestamp > 15 minutes then alert
	#    if size > 100 MB then exec "/my/cleanup/script" as uid dba and gid dba
	#
	#
	## Check directory permission, uid and gid.  An event is triggered if the 
	## directory does not belong to the user with uid 0 and gid 0.  In addition, 
	## the permissions have to match the octal description of 755 (see chmod(1)).
	#
	#  check directory bin with path /bin
	#    if failed permission 755 then unmonitor
	#    if failed uid 0 then unmonitor
	#    if failed gid 0 then unmonitor
	#
	#
	## Check a remote host availability by issuing a ping test and check the 
	## content of a response from a web server. Up to three pings are sent and 
	## connection to a port and an application level network check is performed.
	#
	#  check host myserver with address 192.168.1.1
	#    if failed icmp type echo count 3 with timeout 3 seconds then alert
	#    if failed port 3306 protocol mysql with timeout 15 seconds then alert
	#    if failed url http://user:password@www.foo.bar:8080/?querystring
	#       and content == 'action="j_security_check"'
	#       then alert
	#
	#
	###############################################################################
	## Includes
	###############################################################################
	##
	## It is possible to include additional configuration parts from other files or
	## directories.
	#
	   include /etc/monit/conf.d/*
	#
	deployer@bwelstudy:~$ 
Monit configuration files are located under /etc/monit/ directory…. The main configuration file is /etc/monit/monitrc and has a basic setup configured in it.. You’ll need to make some changes to suit your environment…	example on production:
By default all files located on /etc/monit/conf.d/ and /etc/monit/conf-enabled/ are read by monit when the service is started…  Use the /etc/monit/conf.d/ directory to put all your monitoring configuration files in it…	
You can have as many files as you want… or grop each service into single file….	deployer@bwelstudy:~$ cd /etc/monit/conf.d/
	deployer@bwelstudy:/etc/monit/conf.d$ ls
	memcached  nginx  redis  unicorn  web_interface  websocket_rails
	deployer@bwelstudy:/etc/monit/conf.d$ 
redis	check process redis-server
	    with pidfile "/var/run/redis/redis.pid"
	    start program = "/usr/sbin/service redis-server start"
	    stop program = "/usr/sbin/service redis-server stop"
	    restart program = "/usr/sbin/service redis-server restart"
	    if 2 restarts within 3 cycles then timeout
	    if totalmem > 100 Mb then alert
	    if children > 255 for 5 cycles then stop
	    if cpu usage > 95% for 3 cycles then restart
	    if failed host 127.0.0.1 port 6379 then restart
	    if 5 restarts within 5 cycles then timeout
memcached	check process memcached with pidfile /var/run/memcached.pid
	    start program = "/etc/init.d/memcached start"
	    stop program = "/etc/init.d/memcached stop"
	    if failed host 127.0.0.1 port 11211 then restart
	    if cpu > 70% for 2 cycles then alert
	    if cpu > 98% for 5 cycles then restart
	    if 2 restarts within 3 cycles then timeout
ran monit restart, got error that memcached not installed	https://www.howtoforge.com/how-to-install-memcached-on-ubuntu-1804/
	
	
	apt-get update -y
	apt-get upgrade -y
	
	apt-get install memcached libmemcached-tools -y
	
	systemctl start memcached
	
	systemctl enable memcached
	
	systemctl status memcached
	
	
	
nginx	check process nginx with pidfile /var/run/nginx.pid
	  start program = "/etc/init.d/nginx start"
	  stop program  = "/etc/init.d/nginx stop"
unicorn translate to passenger ? 	unicorn version: 
	check process unicorn with pidfile /home/deployer/harvard_bwel/tmp/unicorn.pid
	  start program "/etc/init.d/unicorn_harvard_bwel start" as uid deployer and gid deployer with timeout 90 seconds
	  restart program "/etc/init.d/unicorn_harvard_bwel restart" as uid deployer and gid deployer with timeout 90 seconds
	  stop program "/etc/init.d/unicorn_harvard_bwel stop" as uid deployer and gid deployer with timeout 90 seconds
	  if 5 restarts within 5 cycles then timeout
	
	
	
	
nginx	
	
	
what ports are in use?	sudo lsof -i -P -n | grep LISTEN
	deploy@ubuntu18hb:~$ sudo lsof -i -P -n | grep LISTEN
	systemd-r  642 systemd-resolve   13u  IPv4  15305      0t0  TCP 127.0.0.53:53 (LISTEN)
	memcached  807        memcache   26u  IPv4  18796      0t0  TCP 127.0.0.1:11211 (LISTEN)
	monit      848            root   11u  IPv4  24773      0t0  TCP 142.93.194.149:2812 (LISTEN)
	sshd       851            root    3u  IPv4  19051      0t0  TCP *:22 (LISTEN)
	sshd       851            root    4u  IPv6  19060      0t0  TCP *:22 (LISTEN)
	postgres   914        postgres    7u  IPv4  19534      0t0  TCP 127.0.0.1:5432 (LISTEN)
	nginx      936            root   10u  IPv4  19378      0t0  TCP *:80 (LISTEN)
	nginx      936            root   11u  IPv4  19379      0t0  TCP *:443 (LISTEN)
	nginx      939        www-data   10u  IPv4  19378      0t0  TCP *:80 (LISTEN)
	nginx      939        www-data   11u  IPv4  19379      0t0  TCP *:443 (LISTEN)
	redis-ser 2168           redis    6u  IPv4  25044      0t0  TCP 127.0.0.1:6379 (LISTEN)
	redis-ser 2168           redis    7u  IPv6  25045      0t0  TCP [::1]:6379 (LISTEN)
	
what is being monitored?	deploy@ubuntu18hb:/etc/monit/conf.d$ sudo monit status
	[sudo] password for deploy: 
	 ?qU:2: Credentials for user admin were already added, entry ignored 'monit'
	Monit 5.25.1 uptime: 17m
	
	Process 'websocket_rails'
	  status                       Initializing
	  monitoring status            Initializing
	  monitoring mode              active
	  on reboot                    start
	  data collected               Wed, 12 Feb 2020 14:10:06
	
	Process 'redis-server'
	  status                       Not monitored
	  monitoring status            Not monitored
	  monitoring mode              active
	  on reboot                    start
	  data collected               Wed, 12 Feb 2020 14:02:36
	
	Process 'nginx'
	  status                       OK
	  monitoring status            Monitored
	  monitoring mode              active
	  on reboot                    start
	  pid                          935
	  parent pid                   1
	  uid                          0
	  effective uid                0
	  gid                          0
	  uptime                       14m
	  threads                      1
	  children                     1
	  cpu                          0.0%
	  cpu total                    0.0%
	  memory                       0.3% [5.6 MB]
	  memory total                 1.1% [21.0 MB]
	  security attribute           unconfined
	  data collected               Wed, 12 Feb 2020 14:10:06
	
	Process 'memcached'
	  status                       Not monitored
	  monitoring status            Not monitored
	  monitoring mode              active
	  on reboot                    start
	  data collected               Wed, 12 Feb 2020 14:02:36
	
	System 'ubuntu18hb'
	  status                       OK
	  monitoring status            Monitored
	  monitoring mode              active
	  on reboot                    start
	  load average                 [0.05] [0.06] [0.06]
	  cpu                          0.8%us 0.4%sy 0.1%wa
	  memory usage                 107.9 MB [5.4%]
	  swap usage                   0 B [0.0%]
	  uptime                       17m
	  boot time                    Wed, 12 Feb 2020 13:54:51
	  data collected               Wed, 12 Feb 2020 14:10:06
why isn't redis being monitored?	https://stackoverflow.com/questions/32099509/monit-not-working-with-redis
	https://redis.io/topics/quickstartd
	
	it is now, that the PID file name is correct
why isn't memcache being monitored?	ps aux | grep memcached
	deploy@ubuntu18hb:/etc/monit/conf.d$ ps aux | grep memcached
	memcache   818  0.0  0.1 424764  2984 ?        Ssl  Feb12   0:11 /usr/bin/memcached -m 64 -p 11211 -u memcache -l 127.0.0.1 -P /var/run/memcached/memcached.pid
	deploy   25341  0.0  0.0  14856  1032 pts/0    S+   13:12   0:00 grep --color=auto memcached
	deploy@ubuntu18hb:/etc/monit/conf.d$ cat memcached 
	check process memcached with pidfile /var/run/memcached.pid
	    start program = "/etc/init.d/memcached start"
	    stop program = "/etc/init.d/memcached stop"
	    if failed host 127.0.0.1 port 11211 then restart
	    if cpu > 70% for 2 cycles then alert
	    if cpu > 98% for 5 cycles then restart
	    if 2 restarts within 3 cycles then timeout
	deploy@ubuntu18hb:/etc/monit/conf.d$ 
	
	
	same problem: PID file is not referenced correctly
what is up with websocket_rails?	https://www.freecodecamp.org/news/implementing-web-sockets-in-a-rails-4-fb45696f8d8c/found a reference to it in app/taperole/roles/websocket_monit/templates
	        check process websocket_rails with pidfile {{ be_app_path }}/tmp/pids/websocket_rails.pid
	                  start program = "/bin/bash -lc 'cd {{ be_app_path }} && /usr/local/rbenv/shims/bundle exec rake websocket_rails:start_server RAILS_ENV={{ be_app_env }}'" as uid deployer
	                  stop program = "/bin/bash -lc 'cd {{ be_app_path }} && /usr/local/rbenv/shims/bundle exec rake websocket_rails:stop_server RAILS_ENV={{ be_app_env }}'" as uid deployer
	                ran this cmd on Ubuntu server:
	
	deploy@ubuntu18hb:~/harvard_bwel/current$ bundle exec rake websocket_rails:start_server RAILS_ENV=production
	Websocket Rails Standalone Server listening on port 3001
	/home/deploy/harvard_bwel/shared/bundle/ruby/2.4.0/gems/thin-1.6.4/lib/thin/server.rb:107: warning: constant ::Fixnum is deprecated
	
show all PID on Ubuntu	ps aux
	
	redis     1358  0.1  0.1  54396  3600 ?        Ssl  14:26   0:00 /usr/bin/redis-server 127.0.0.1:6379
	
	I think I have installed redis-server under /usr/bin when it should not be there
	
	I think it might need to be installed from root
redis.pid vs redis-server.pid	there is a file with a pid number here:                               /var/run/redis/redis-server.pid
	
	the monit status seems to be checking for a file called:   /var/run/redis/redis.pd
	
	edited this file:   :/etc/monit/conf.d/redis
	
	changed this: 
	
	
	check process redis-server
	    with pidfile "/var/run/redis/redis.pid"
	    start program = "/usr/sbin/service redis-server start"
	    stop program = "/usr/sbin/service redis-server stop"
	    restart program = "/usr/sbin/service redis-server restart"
	    if 2 restarts within 3 cycles then timeout
	    if totalmem > 100 Mb then alert
	    if children > 255 for 5 cycles then stop
	    if cpu usage > 95% for 3 cycles then restart
	    if failed host 127.0.0.1 port 6379 then restart
	    if 5 restarts within 5 cycles then timeout
	
	
	to this:
	
	
	check process redis-server
	    with pidfile "/var/run/redis/redis-server.pid"
	    start program = "/usr/sbin/service redis-server start"
	    stop program = "/usr/sbin/service redis-server stop"
	    restart program = "/usr/sbin/service redis-server restart"
	    if 2 restarts within 3 cycles then timeout
	    if totalmem > 100 Mb then alert
	    if children > 255 for 5 cycles then stop
	    if cpu usage > 95% for 3 cycles then restart
	    if failed host 127.0.0.1 port 6379 then restart
	    if 5 restarts within 5 cycles then timeout
	
	
	
	
	
what tells monit to start monitoring?	
	
	
	
Ansible .j2 files	https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html
	
	
read about nginx	
Twilio websocket	
Deploy to AWS sandbox	
Bump Rails version test	
nginx PPA	
md5	
daemon	
	
