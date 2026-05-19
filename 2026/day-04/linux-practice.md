* Process Commands: A process is any running program. Every process has a PID.

1. ps aux 
> jeenicj@DESKTOP-BG3MAVI:~$ ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.1  0.3  22528 12996 ?        Ss   08:02   0:17 /sbin/init
root           2  0.0  0.0   3120  1920 ?        Sl   08:02   0:00 /init
root           6  0.0  0.0   3136  1792 ?        Sl   08:02   0:00 plan9 --control-socket 7 --log-level 4 --server-fd 8 --pipe-fd 10

2. pgrep OR ps aux | grep <name>
> jeenicj@DESKTOP-BG3MAVI:~$ pgrep -a cron
  216 /usr/sbin/cron -f -P


* Service Commands: A service(or daemon) is a background process managed by systemd.

1. systemctl status cron
> jeenicj@DESKTOP-BG3MAVI:~$ systemctl status cron
● cron.service - Regular background program processing daemon
     Loaded: loaded (/usr/lib/systemd/system/cron.service; enabled; preset: enabled)
     Active: active (running) since Tue 2026-05-19 06:33:51 UTC; 5h 4min ago
       Docs: man:cron(8)

2. systemctl list-units --type=service --state=running
> jeenicj@DESKTOP-BG3MAVI:~$ systemctl list-units --type=service --state=running
  UNIT                                          LOAD   ACTIVE SUB     DESCRIPTION
  console-getty.service                         loaded active running Console Getty
  containerd.service                            loaded active running containerd container runtime
  cron.service                                  loaded active running Regular background program processing daemon
  dbus.service                                  loaded active running D-Bus System Message Bus
  docker.service                                loaded active running Docker Application Container Engine


* Log Commands: journalctl is systemd's log viewer. Every service writes logs that journalctl can read

1. journalctl -u cron --no-pager | tail -5
> jeenicj@DESKTOP-BG3MAVI:~$ journalctl -u cron --no-pager | tail -5
May 19 08:17:04 DESKTOP-BG3MAVI CRON[151799]: pam_unix(cron:session): session closed for user root
May 19 09:17:04 DESKTOP-BG3MAVI CRON[187116]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 19 09:17:04 DESKTOP-BG3MAVI CRON[187116]: pam_unix(cron:session): session closed for user root
May 19 11:17:01 DESKTOP-BG3MAVI CRON[300455]: pam_unix(cron:session): session opened for user root(uid=0) by root(uid=0)
May 19 11:17:01 DESKTOP-BG3MAVI CRON[300455]: pam_unix(cron:session): session closed for user root

2. journalctl -n 5 --no-pager
> jeenicj@DESKTOP-BG3MAVI:~$ journalctl -n 5 --no-pager
May 19 11:45:31 DESKTOP-BG3MAVI wsl-pro-service[315]: INFO Daemon: connecting to Windows Agent
May 19 11:45:31 DESKTOP-BG3MAVI wsl-pro-service[315]: DEBUG Updated systemd status to "Connecting"
May 19 11:45:31 DESKTOP-BG3MAVI wsl-pro-service[315]: WARNING Daemon: could not connect to Windows Agent: could not get address: could not read agent port file "/mnt/c/Users/Jeeni/.ubuntupro/.address": open /mnt/c/Users/Jeeni/.ubuntupro/.address: no such file or directory
May 19 11:45:31 DESKTOP-BG3MAVI wsl-pro-service[315]: INFO Reconnecting to Windows host in 60 seconds
May 19 11:45:31 DESKTOP-BG3MAVI wsl-pro-service[315]: DEBUG Updated systemd status to "Not connected: waiting to retry"

