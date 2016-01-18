# perfstats-to-syslog

perfstats-to-syslog agent aims at getting CPU, Memory, Disk and Network statistics pushed in proper JSON log format to a local syslog agent.
It runs over Linux and Windows environments.

## How to install?

The following instructions is working only against Linux environments.

### Dependencies

```
$ sudo apt-get install python-dev
$ sudo pip install psutil
```

### Install the binaries

Drop `perfstats-to-syslog.cfg` and `perfstats-to-syslog.py` into the `/opt/perfstats-to-syslog` directory.

### Install the upstart service

Drop `perfstats-to-syslog.conf` into the `/init` directory.

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
pollingInSec = 60

[general]
paths = root:/
```

You can configure:
- the host and port of the syslog agent
- the polling period => 60 seconds by default
- paths of the disks you want to monitor

Ready?

### Start the service

To start the service:
```
$ sudo start perfstats-to-syslog
```

As configured the service gets started at every reboot of your machine.
