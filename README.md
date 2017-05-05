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

Requires Python and [pika](https://pypi.python.org/pypi/pika).
