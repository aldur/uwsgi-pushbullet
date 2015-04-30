# uWSGI-pushbullet
uWSGI-pushbullet is a plugin to send notifications through the [Pushbullet](https://www.pushbullet.com/) service.

## Features
uWSGI-pushbullet provides the following features:

* Registers _pushbullet alarm_ and _pushbullet hook_.
* Supports the whole [Pushbullet API](https://docs.pushbullet.com/) (send pushes to channels, devices, emails and so on).

## Installation
This plugin requires:
* [libcurl](http://curl.haxx.se/libcurl/) (to send HTTP requests with ease)
* [jansson](https://github.com/akheron/jansson) (for JSON parsing)

Please follow the specific documentation on how to install them.

This plugin is 2.0 friendly.
You can thus build it with:
```bash
$ git clone https://github.com/aldur/uwsgi-pushbullet
$ uwsgi --build-plugin uwsgi-pushbullet
```

## Configuration
To use this plugin you'll need your [Pushbullet API access token](https://www.pushbullet.com/account).

You can configure the alarms in your app as follows:
```ini
[uwsgi]
plugins = pushbullet

; register a 'pushme' alarm
alarm = pushme pushbullet:token=<YOUR_API_TOKEN>,title=Alarm!
; raise alarms no more than 1 time per minute (default is 3 seconds)
alarm-freq = 60

; raise an alarm whenever uWSGI segfaults
alarm-segfault = pushme

; raise an alarm whenever /danger is hit
route = ^/danger alarm:pushme /danger has been visited!

; raise an alarm when the avergae response time is higher than 3000 milliseconds
metric-alarm = key=worker.0.avg_response_time,value=3000,alarm=pushme

; ...
```
The only mandatory key-value field needed to send an alarm is the token.
In the previous example we've also set the push title.

Hooks, on the other side, require a body too:
```ini
[uwsgi]
plugins = pushbullet

hook-post-app = pushbullet:token=<YOUR_API_TOKEN>,body=Your app has been loaded.

; ...
```

### Key-value fields
We support the entire [Pushbullet HTTP API](https://docs.pushbullet.com/#pushes).
Available key-values fields include:
* title
* body
* url
* device_iden
* email
* channel_tag
* client_iden
* source_device_iden

We automatically infer the push `type` by using the URL field.

On the uWSGI/networking side you can set:
* timeout: specifies the socket timeout.
* ssl_no_verify: tells Curl to not verify the server SSL certificate.
