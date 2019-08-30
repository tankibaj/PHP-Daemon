## Run PHP without browser


You can run PHP Script from Linux terminal without the need of any browser. Run the PHP file located at <code>/var/www/html/infophp.php</code> in Linux Command Line as

```bash
php -f /var/www/html/infophp.php | less
```

Or

```bash
php -f /var/www/html/infophp.php
```

Here option <code>-f</code> parse and execute the file that follows the command.


## Run PHP executable

You can run a PHP script simply as, if it is a shell script. First Create a PHP sample script in your current working directory.

```bash
echo -e '#!/usr/bin/php\n<?php phpinfo(); ?>' > phpscript.php
```

Notice we used <code>#!/usr/bin/php</code> in the first line of this PHP script as we use to do in shell script <code>(/bin/bash)</code>. The first line <code>#!/usr/bin/php</code> tells the Linux Command-Line to parse this script file to PHP Interpreter.

Second make it executable as

```shell
chmod 755 phpscript.php
```

and run it as,

```shell
./phpscript.php
```

## PHP script as a daemon/service

Assume we need to permanently run a PHP CLI script, it need to run as a daemon/service. It will support service <code>start</code> <code>stop</code> commands. Okay lets start.

Create a daemon/service called:

```shell
sudo touch /etc/init.d/myPHPservice
```

Open created daemon/service:

```shell
sudo nano /etc/init.d/myPHPservice
```

And paste following code

```sh
#! /bin/sh

# Installation
# - Paste these to /etc/init.d/myPHPservice
# - chmod +x this
#
# Starting and stopping
# - Start: service myPHPservice start OR /etc/init.d/myPHPservice start
# - Stop: service myPHPservice stop OR /etc/init.d/myPHPservice stop



NAME=myPHPservice
DESC="Daemon for PHP CLI script"
PIDFILE="/var/run/${NAME}.pid"
LOGFILE="/var/log/${NAME}.log"

DAEMON="/usr/bin/php"
DAEMON_OPTS="/path/to/your/php/script.php"

START_OPTS="--start --background --make-pidfile --pidfile ${PIDFILE} --exec ${DAEMON} ${DAEMON_OPTS}"
STOP_OPTS="--stop --pidfile ${PIDFILE}"

test -x $DAEMON || exit 0

set -e

case "$1" in
    start)
        echo -n "Starting ${DESC}: "
        start-stop-daemon $START_OPTS >> $LOGFILE
        echo "$NAME."
        ;;
    stop)
        echo -n "Stopping $DESC: "
        start-stop-daemon $STOP_OPTS
        echo "$NAME."
        rm -f $PIDFILE
        ;;
    restart|force-reload)
        echo -n "Restarting $DESC: "
        start-stop-daemon $STOP_OPTS
        sleep 1
        start-stop-daemon $START_OPTS >> $LOGFILE
        echo "$NAME."
        ;;
    *)
        N=/etc/init.d/$NAME
        echo "Usage: $N {start|stop|restart|force-reload}" >&2
        exit 1
        ;;
esac

exit 0
```

Next, make it executable by running

```shell
chmod +x /etc/init.d/myPHPservice
```

Change <code>NAME</code>, <code>DESCRIPTION</code>, <code>DAEMON</code>, and <code>DAEMON_OPTS</code> to reflect your setup.

Once done you can start the service by running <code>/etc/init.d/myPHPservice</code> start and stop it by running <code>/etc/init.d/myservice stop</code>. Restarting is also possible by running <code>/etc/init.d/myservice restart</code>.

Verify the running of the daemon by checking the contents of the created PID file: <code>cat /var/run/myservice.pid</code> — It should contain a process ID.
