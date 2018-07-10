# icinga2-eventhandler-cachet

A icinga2 event handler to push icinga2 notifications to Cachet API

## Prerequisites

* Have a working Cachet installation (last tested cachet 2.2)
* Have created some Cachet components to interact with
* Have an icinga2 instance with PHP CLI and php-curl installed

## Installation

* Get a Cachet API key: Create a new user in Cachet dashboard, login with this user, and get the API key in his profile.
* Copy cachet_notify to /etc/icinga2/scripts (depending on your configuration)
* Change URL and API key in cachet_notify source code
* Try it: `./cachet_notify 'My Cachet component' 'My icinga2 service' CRITICAL HARD 'The service is Critical'`

## Configuration

* In the file: `/etc/icinga2/conf.d/commands.conf`
```
object NotificationCommand "cachet-notifications" {
    command = [ SysconfDir + "/icinga2/scripts/cachet-notifications" ]
    env = {
        SERVICEDESC = "$service.name$"
        SERVICESTATE = "$service.state$"
    }
}
```

* In the file: `/etc/icinga2/conf.d/notifications.conf`
```
apply Notification "cachet-notifications-notification" to Service {
  import "cachet-notifications-configuration"

  assign where service.vars.cachet_notifications == "enabled"

}
```

* In the file: `/etc/icinga2/conf.d/templates.conf`
```
template Service "generic-service" {
  max_check_attempts = 5
  check_interval = 1m
  retry_interval = 30s

  vars.cachet_notifications = "enabled"
}

template Notification "cachet-notifications-configuration" {
    command = "cachet-notifications"

    users = [ "icingaadmin" ]
    states = [ OK, Warning, Critical]
    period = "24x7"
}
```


 - Add a script on your scripts folder: `/etc/icinga2/scripts/cachet-notifications`
```
	#!/bin/sh

	/etc/icinga2/scripts/cachet_notify $SERVICEDESC $SERVICEDESC $SERVICESTATE
``` 
 - Reload icinga2 configuration
