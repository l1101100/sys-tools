#!/bin/bash
#
# Jdis - Java daemon init-script for Red Hat / CentOS Linux
# Ali Erdinc Koroglu - http://ae.koroglu.org
# License : GNU GPL (http://www.gnu.org/licenses/gpl.html)
#
# You must install daemonize package from EPEL repository to use this script.
# How to add EPEL repository: http://ae.koroglu.org/docs/adding-epel-repository-on-centos/
#
# History:
# 2014-08-19 : First release

# chkconfig: 345 99 05
# description: Java daemon script

### BEGIN INIT INFO
# Provides:
# Required-Start: $local_fs $network $syslog $time
# Required-Stop: $local_fs $network $syslog $time
# Short-Description: start and stop Java daemons
# Description: Java daemon init script
### END INIT INFO

# source function library
. /etc/init.d/functions

# Java Home
java_home="/usr/java/jdk1.7.0_45"                                               # java path

# Service settings
service_name="websocket"                                                        # Service name
service_user="farmer"                                                           # User/group of process
pid_file="/var/run/$service_name.pid"                                           # Pid file
log_file="/var/log/$service_name/$service_name.log"                             # StdOut log file
errlog_file="/var/log/$service_name/$service_name-error.log"                    # StdErr log file
java="$java_home/bin/java"                                                      # Java binary
java_appdir="/usr/share/pronet/websocket"                                       # Application path
java_arg1="-server -Dfile.encoding=utf-8"                                       # Argument 1
java_arg2="-Dproject.properties=$java_appdir/project.properties"                # Argument 2
java_arg3="-Dlog4j.configuration=file:$java_appdir/log4j.properties"            # Argument 3
java_arg4="-jar $java_appdir/pronetgaming-nettosphere-websocket.jar"            # Argument 4
java_arg5="-Xms512m -Xmx4Gm -Xss4M -XX:PermSize=256m -XX:MaxPermSize=256m"      # Argument 5
java_args="$java_arg1 $java_arg2 $java_arg3 $java_arg4 $java_arg5               # Java arguments


RETVAL=0
start() {
        [ -x $java ] || exit 5
        echo -n $"Starting $service_name: "
        if [ $EUID -ne 0 ]; then
            RETVAL=1
            failure
        elif [ -s /var/run/$service_name.pid ]; then
            RETVAL=1
            echo -n $"already running.."
            failure
        else
            daemonize -u $service_user -p $pid_file -o $log_file -e $errlog_file -c $java_appdir $java $java_args && success || failure
            RETVAL=$?
            [ $RETVAL -eq 0 ] && touch /var/lock/subsys/$service_name
        fi;
        echo
        return $RETVAL
}

stop() {
        echo -n $"Stopping $service_name: "
        if [ $EUID -ne 0 ]; then
                RETVAL=1
                failure
        else
                killproc -p $pid_file
                RETVAL=$?
                [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/$service_name
        fi;
        echo
        return $RETVAL
}

restart(){
        stop
        start
}


case "$1" in
  start)
        start
        RETVAL=$?
        ;;
  stop)
        stop
        RETVAL=$?
        ;;
  restart)
        restart
        RETVAL=$?
        ;;
  status)
        status $service_name
        RETVAL=$?
        ;;
  *)
        echo $"Usage: $0 {start|stop|status|restart}"
        RETVAL=2
esac
exit $RETVAL
