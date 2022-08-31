# 1、sysv-rc-conf简介
简单来说，就是linux上面一个很好用的管理开机启动的软件。

安装：
```
sudo apt-get install sysv-rc-conf
```
你大概率是没有源的，如果没有源：
```
编辑软件源列表：sudo vim /etc/apt/sources.list
末尾添加软件源：deb http://archive.ubuntu.com/ubuntu/ trusty main universe restricted multiverse
更新apt-get：sudo apt-get update
```
启动（带操作界面）：
```
sudo sysv-rc-conf
```
启动（单纯查看）：
```
sysv-rc-conf --list
```


# 2、查看是否开机启动
```
root@fatpo:~# sysv-rc-conf --list mysql
mysql        0:off    1:off    2:on    3:on    4:on    5:on    6:off
root@fatpo:~# sysv-rc-conf --list redis-server
redis-server 0:off    1:off    2:on    3:on    4:on    5:on    6:off
root@fatpo:~# sysv-rc-conf --list nginx
nginx        0:off    1:off    2:on    3:on    4:on    5:on    6:off
```
至此，看到3个关键的服务nginx,mysql,redis，都有2，3，4，5级别的开机启动，就很安心啦~

看来ubuntu18.04之前安装这些软件时，已经帮我们这些小白顺便搞定了开启启动，没这么蠢~

# 3、添加开机启动的demo
```
sudo sysv-rc-conf --level 2345 mysql on
```

# 4、安装supervisor
有两条路子：
* apt-get install supervisor
* pip3 install supervisor==4.2.2

但是实操来看，用pip3安装的，总是无法开机自启动，非常疑惑，建议大家就用`apt-get install`。

# 5、添加开机启动
```
sudo sysv-rc-conf --level 2345 supervisord on
```

检查下 `cat /etc/init.d/supervisor` ，如果不存在创建之：
```
#! /bin/sh
#
# skeleton      example file to build /etc/init.d/ scripts.
#               This file should be used to construct scripts for /etc/init.d.
#
#               Written by Miquel van Smoorenburg <miquels@cistron.nl>.
#               Modified for Debian
#               by Ian Murdock <imurdock@gnu.ai.mit.edu>.
#               Further changes by Javier Fernandez-Sanguino <jfs@debian.org>
#
# Version:      @(#)skeleton  1.9  26-Feb-2001  miquels@cistron.nl
#
### BEGIN INIT INFO
# Provides:          supervisor
# Required-Start:    $remote_fs $network $named
# Required-Stop:     $remote_fs $network $named
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start/stop supervisor
# Description:       Start/stop supervisor daemon and its configured
#                    subprocesses.
### END INIT INFO

. /lib/lsb/init-functions

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DAEMON=/usr/local/bin/supervisord
NAME=supervisord
DESC=supervisor

test -x $DAEMON || exit 0

LOGDIR=/var/log/supervisor
PIDFILE=/var/run/$NAME.pid
DODTIME=5                   # Time to wait for the server to die, in seconds
                            # If this value is set too low you might not
                            # let some servers to die gracefully and
                            # 'restart' will not work

# Include supervisor defaults if available
if [ -f /etc/default/supervisor ] ; then
        . /etc/default/supervisor
fi
DAEMON_OPTS="-c /etc/supervisor/supervisord.conf $DAEMON_OPTS"

set -e

running_pid()
{
    # Check if a given process pid's cmdline matches a given name
    pid=$1
    name=$2
    [ -z "$pid" ] && return 1
    [ ! -d /proc/$pid ] &&  return 1
    (cat /proc/$pid/cmdline | tr "\000" "\n"|grep -q $name) || return 1
    return 0
}

running()
{
# Check if the process is running looking at /proc
# (works for all users)

    # No pidfile, probably no daemon present
    [ ! -f "$PIDFILE" ] && return 1
    # Obtain the pid and check it against the binary name
    pid=`cat $PIDFILE`
    running_pid $pid $DAEMON || return 1
    return 0
}

force_stop() {
# Forcefully kill the process
    [ ! -f "$PIDFILE" ] && return
    if running ; then
        kill -15 $pid
        # Is it really dead?
        [ -n "$DODTIME" ] && sleep "$DODTIME"s
        if running ; then
            kill -9 $pid
            [ -n "$DODTIME" ] && sleep "$DODTIME"s
            if running ; then
                echo "Cannot kill $LABEL (pid=$pid)!"
                exit 1
            fi
        fi
    fi
    rm -f $PIDFILE
    return 0
}

case "$1" in
  start)
        echo -n "Starting $DESC: "
        start-stop-daemon --start --quiet --pidfile $PIDFILE \
                --startas $DAEMON -- $DAEMON_OPTS
        test -f $PIDFILE || sleep 1
        if running ; then
            echo "$NAME."
        else
            echo " ERROR."
        fi
        ;;
  stop)
        echo -n "Stopping $DESC: "
        start-stop-daemon --stop --quiet --oknodo --pidfile $PIDFILE
        echo "$NAME."
        ;;
  force-stop)
        echo -n "Forcefully stopping $DESC: "
        force_stop
        if ! running ; then
            echo "$NAME."
        else
            echo " ERROR."
        fi
        ;;
  #reload)
        #
        #       If the daemon can reload its config files on the fly
        #       for example by sending it SIGHUP, do it here.
        #
        #       If the daemon responds to changes in its config file
        #       directly anyway, make this a do-nothing entry.
        #
        # echo "Reloading $DESC configuration files."
        # start-stop-daemon --stop --signal 1 --quiet --pidfile \
        #       /var/run/$NAME.pid --exec $DAEMON
  #;;
  force-reload)
        #
        #       If the "reload" option is implemented, move the "force-reload"
        #       option to the "reload" entry above. If not, "force-reload" is
        #       just the same as "restart" except that it does nothing if the
        #   daemon isn't already running.
        # check wether $DAEMON is running. If so, restart
        start-stop-daemon --stop --test --quiet --pidfile $PIDFILE \
        --startas $DAEMON \
        && $0 restart \
        || exit 0
        ;;
  restart)
    echo -n "Restarting $DESC: "
    start-stop-daemon --stop --quiet --oknodo --pidfile $PIDFILE
        [ -n "$DODTIME" ] && sleep $DODTIME
        start-stop-daemon --start --quiet --pidfile $PIDFILE \
                --startas $DAEMON -- $DAEMON_OPTS
        echo "$NAME."
        ;;
  status)
    echo -n "$LABEL is "
    if running ;  then
        echo "running"
    else
        echo " not running."
        exit 1
    fi
    ;;
  *)
        N=/etc/init.d/$NAME
        # echo "Usage: $N {start|stop|restart|reload|force-reload}" >&2
        echo "Usage: $N {start|stop|restart|force-reload|status|force-stop}" >&2
        exit 1
        ;;
esac

exit 0

```
添加权限：
```
chmod +x /etc/init.d/supervisor
```


# 6、想进一步了解开机启动的这边


[fatpo.github.io/#/我干运维那些事/生产实战/linux私房菜/ubuntu18.04用sysv-rc-conf管理开机自启动](https://fatpo.github.io/#/%E6%88%91%E5%B9%B2%E8%BF%90%E7%BB%B4%E9%82%A3%E4%BA%9B%E4%BA%8B/%E7%94%9F%E4%BA%A7%E5%AE%9E%E6%88%98/linux%E7%A7%81%E6%88%BF%E8%8F%9C/ubuntu18.04%E7%94%A8sysv-rc-conf%E7%AE%A1%E7%90%86%E5%BC%80%E6%9C%BA%E8%87%AA%E5%90%AF%E5%8A%A8)


