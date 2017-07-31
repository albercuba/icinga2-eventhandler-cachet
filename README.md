# icinga2-eventhandler-cachet
A icinga2 event handler to push icinga2 notifications to Cachet API

## Prerequisites
 - Have a working Cachet installation (last tested cachet 2.2)
 - Have created some Cachet components to interact with
 - Have an icinga2 instance with PHP CLI and php-curl installed

## Installation

 - Get a Cachet API key: Create a new user in Cachet dashboard, login with this user, and get the API key in his profile.
 - Copy cachet_notify to /etc/icinga2/scripts (depending on your configuration)
 - Change URL and API key in cachet_notify source code
 - Try it: `./cachet_notify 'My Cachet component' 'My icinga2 service' CRITICAL HARD 'The service is Critical'`

## Configuration

 - Make a Icinga2 command: `/etc/icinga2/conf.d/commands.conf`
```
	object NotificationCommand "cachet-incident-notification" {
		command = [ SysconfDir + "/icinga2/scripts/cachet-incident-notification" ]
		env = {
			SERVICEDESC = "$service.name$"
			SERVICESTATE = "$service.state$"
		}
	}
```
 - Add a script on your scripts folder (cachet-incident-notification): `/etc/icinga2/scripts/cachet-incident-notification`
```
	#!/bin/sh

	/etc/icinga2/scripts/cachet_notify $SERVICEDESC $SERVICEDESC $SERVICESTATE
``` 
 - Reload icinga2 configuration
