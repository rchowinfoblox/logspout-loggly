# logspout-loggly
Logspout container for Docker and Loggly.

[This repo follows this suggested pattern from logspout](https://github.com/gliderlabs/logspout/tree/master/custom)

You can build the image yourself

```docker build -t your-user-name/logspout-loggly ./```

and, optionally, push it to your own hub.docker.com repo

```docker push your-user-name/logspout-loggly```

or you can pull a prebuilt image

```docker pull iamatypeofwalrus/logspout-loggly```

## How to run

```sh
docker run -d \
  -e 'LOGGLY_TOKEN=<token>' \
  -e 'LOGGLY_TAGS=<comma-delimited-list>' \
  -e 'FILTER_NAME=<wildcard-container-name>' \
  --volume /var/run/docker.sock:/tmp/docker.sock \
  iamatypeofwalrus/logspout-loggly
```

### Overriding Logged json.hostname
By default, the json.hostname sent to Loggly is the docker container's notion of the hostname.
However, this hostname may not be accurate.
For example, containers launched by Mesos framework can have hostnames that are gibberish
(actually the gibberish is the first 10 characters of the container_id).

You can override the default hostname with the hostname defined by a environment variable defined
within the Logspout-Loggly container:

```sh
docker run -d \
  -e 'LOGGLY_TOKEN=<token>' \
  -e 'LOGGLY_TAGS=<comma-delimited-list>' \
  -e 'FILTER_NAME=<wildcard-container-name>' \
  -e 'LOGGLY_JSON_HOSTNAME_VAR=<hostname-environment-variable>' \
  --volume /var/run/docker.sock:/tmp/docker.sock \
  iamatypeofwalrus/logspout-loggly
```

For example, you could specify `-e 'LOGGLY_JSON_HOSTNAME_VAR=HOSTNAME'`

## How it works
Instead of linking containers together or bothering with [syslog or remote syslog](https://www.loggly.com/blog/centralize-logs-docker-containers) this container follows the [12 Factor app logging philosophy](http://12factor.net/logs). If your docker container(s) log(s) to STDOUT this image will pick up that stream from the docker daemon send those events to Loggly.
