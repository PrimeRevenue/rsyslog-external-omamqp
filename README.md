rsyslog-external-omamqp
=======================

Small script to read lines from stdin and publish them to AMQP.

Mostly a proof-of-concept for the new rsyslog feature of external output
modules (http://de.slideshare.net/rainergerhards1/writing-rsyslog-p).

Possibly useful if you need to modify messages. Otherwise use rsyslog's own
`omrabbitmq` instead.

Example configuration in amqp.conf:
```
    $template(name="gelf" type="list") {
    constant(value="{\"version\":\"1.1\",")
    constant(value="\"host\":\"")
    property(name="hostname")
    constant(value="\",\"short_message\":\"")
    property(name="msg" format="json")
    constant(value="\",\"timestamp\":\"")
    property(name="timegenerated" dateformat="unixtimestamp")
    constant(value="\",\"level\":\"")
    property(name="syslogseverity")
    constant(value="\"}")
}
module(load="omprog")
    *.*        action(type="omprog"
                      template="gelf"
                      binary="om_amqp.py --server broker.local ...")
```

```
parser = argparse.ArgumentParser(description='forward logs from stdin to AMQP')
	parser.add_argument('-d', '--debug',    help="print debug messages to logfile",     action='store_true')
	parser.add_argument('-v', '--verbose',  help="print some info messages to logfile", action='store_true')
	parser.add_argument('-l', '--logfile',  help="logfile for verbose and debug output (default: /tmp/om_amqp.py.log)", default='/tmp/om_amqp.py.log')
	parser.add_argument('-f', '--format',   help="input format, either msg or json (default), currently only used for content-type",  default='json')
	parser.add_argument('-u', '--user',     help="AMQP user (default: syslog)",         default='syslog')
	parser.add_argument('-p', '--password', help="AMQP password (default: syslog)",     default='syslog')
	parser.add_argument('-V', '--vhost',    help="AMQP vhost (default: syslog)",        default='syslog')
	parser.add_argument('-q', '--queue',    help="AMQP queue (default: syslog)",        default='syslog')
	parser.add_argument('-e', '--exchange', help="AMQP exchange (default: syslog)",     default='syslog')
	parser.add_argument('-k', '--key',      help="AMQP routing key (default: syslog)",  default='syslog')
	parser.add_argument('-s', '--server',   help="AMQP server (default: amqphost)",     default='amqphost')
	parser.add_argument('-c', '--confirm',  help="enable AMQP confirmed delivery",      action='store_true')
	args = parser.parse_args()
   
```


Requires Python and [pika](https://pypi.python.org/pypi/pika).
