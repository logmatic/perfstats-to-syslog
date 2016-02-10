# perfstats-to-syslog

perfstats-to-syslog is a daemon which collects system performance statistics (CPU, Memory, Disk and Network) periodically and streams it in clean JSON format to a syslog agent
It runs over Linux and Windows environments.

## How to install?

The following instructions is working only against Linux environments.

### Dependencies

```
$ sudo apt-get install python-dev python-pip
$ sudo pip install psutil
```

### Install script & configuration files

Drop `perfstats-to-syslog.cfg` and `perfstats-to-syslog.py` into the `/opt/perfstats-to-syslog` directory.

### Set up the service

#### With Upstart (check `/upstart` directory):

Drop `perfstats-to-syslog.conf` into the `/etc/init` directory.

#### Or with init.d (check `/init.d` directory):

Drop `perfstats-to-syslog` into the `/etc/init.d` directory.

## Don't forget to configure your syslog agent! (Rsyslog or Syslog-NG)

Your syslog agent must listen UDP on port 514.

### Over Rsyslog

In `/etc/rsyslog.conf`, you should ensure that the 2 following lines are uncommented:

```
$ModLoad imudp
$UDPServerRun 514
```

And restart the service.

### Over Syslog-NG

In `/etc/syslog-ng/syslog-ng.conf`:

- Add a UDP source:

```
source udp_src { udp(ip(0.0.0.0) port(514)); };
```

- Don't forget to wire it to a destination:

```
log { source(udp_src); destination(d_logmatic); };
```

## Configure and start the service

### Configuration file

Your configuration file should now be located at `/opt/perfstats-to-syslog/perfstats-to-syslog.cfg`

You should see something like:

```
[syslog]
host = 127.0.0.1
port = 514
pollingInSec = 5

[general]
paths = root:/
```

You can configure:
- the host and port of the syslog agent
- the polling period => 5 seconds by default
- paths of the disks you want to monitor

Ready?

### Start the service

To start the service with upstart:
```
$ sudo start perfstats-to-syslog
```

Or if you installed the service with init.d:
```
$ sudo /etc/init.d/perfstats-to-syslog start
```

As configured the service gets started at every reboot of your machine.
