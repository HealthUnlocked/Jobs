HealthUnlocked coding challenge 2015
------------------------------------

You are tasked with building a monitoring app that checks the state of various services at regular intervals and reports back with latest findings. This is a self-hosted application, capable of both sending and receiving HTTP requests, it reads configuration from a provided settings file and keeps an in-memory state of all monitored services.

### Initialising

Read the settings file and add the info to a static variable. Initial state for all services is "OK", initial time since last seen is 0.

### Status

Create an endpoint `/status`, that will return the current status of all monitored services using GET request in the following format:

```json
{
    "payload": [
        {
            "name": "My Web Application",
            "status": "OK",
            "last_seen": 0
        },
        {
            "name": "My Console Application",
            "status": "OK",
            "last_seen": 0
        }
    ]
}
```

### Monitoring

For each service with type `daemon` expect a PUT request to `/status` endpoint supplying `id` parameter, this will reset the `last_seen` variable for corresponding service whenever received. `curl -XPUT localhost:8888/status -d id=my-daemon-service` should simply return "OK".

For each service with type `web` issue a GET request to `url` every second. If request is successful, i.e. the response is "OK", reset the `last_seen` variable to 0, otherwise increment it by 1.
To simulate a web service, you need to create `/ping` endpoint that will return "OK" with 30% success rate, and "SERVER ERROR" the rest of the time.

### Alerting

Create a background thread that checks whether each service's `last_seen` variable value exceeds its `alert` threshold, if it does the status should be changed to "ALERT", if it does not, it should be "OK".

### Settings file

```json
[{
	"id": "my-web-app",
	"type": "web",
	"name": "My Web Application",
	"alert": 10,
	"url": "http://localhost:8888/ping"
},{
	"id": "my-daemon-service",
	"type": "daemon",
	"name": "My Console Application",
	"alert": 10
}]
```
