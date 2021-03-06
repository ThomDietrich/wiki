## Docker images are available on [Docker Hub](https://hub.docker.com/r/golift/unifi-poller/tags)

Many thanks to [mabunixda](https://github.com/mabunixda) for
[helping](https://github.com/unifi-poller/unifi-poller/pull/38) begin our Docker support!
The images are [built automatically](https://cloud.docker.com/repository/docker/golift/unifi-poller/builds)
by Docker Cloud using the
[Dockerfile](https://github.com/unifi-poller/unifi-poller/blob/master/init/docker/Dockerfile)
included in this repo.

### Pulling Images

Linux images are available for 386, amd64, arm32v6 and arm64v8 architectures.
There is no need to specify an architecture tag, docker will pull the correct
image automatically with the `latest` tag.

You can install `latest` (recommended), or pick a specific version.
See the following sections for information on how to do each.

#### Stable Release

*   **This is the recommended way to install.**

Install the current stable released version using a tag like this:

```shell
docker pull golift/unifi-poller:latest
```

#### Latest (master)

Download the latest possibly-unreleased code
with this command (using the `master` tag):

```shell
docker pull golift/unifi-poller:master
```

Using `master` is not recommended. You may be asked to give this a try while troubleshooting
or debugging, but generally this will contain untested code or things that will break your graphs.
The `master` docker tag is based from the `master` git branch and may contain bugs; you've been overly warned.

#### Specific version

Install a specific version like this:

```shell
docker pull golift/unifi-poller:2.0.1
```

#### Major or Minor version

Install the latest minor version like this:

```shell
docker pull golift/unifi-poller:2.0
```

And the latest major version (2) like this:

```shell
docker pull golift/unifi-poller:2
```

The former will download the latest 2.0.x release, and the latter will download
the latest 2.x release. These are both considered safe and OK.

#### From Source

You can build your own image from source.

```shell
git clone https://github.com/unifi-poller/unifi-poller.git
cd unifi-poller
make docker
```

This builds a 64-bit amd64 linux image from scratch. If you need another architecture,
use the `docker build` command directly with a correct `--build-arg` flag.
[Examples here](https://github.com/unifi-poller/unifi-poller/tree/master/init/docker/hooks).

## Running the Container

*   **Make sure you've completed the prerequisites in the [Installation](Installation) article.**
*   This command starts the container as a daemon:

```shell
docker pull golift/unifi-poller
docker run -d -v /host/path/up.conf:/config/unifi-poller.conf golift/unifi-poller
```

*   Copy the [example config file](https://github.com/unifi-poller/unifi-poller/blob/master/examples/up.conf.example)
    from this repo and mount it as an overlay into the container.
*   You should mount your overlay config file at **/config/unifi-poller.conf**.
*   _For legacy reasons, you may also mount it at /etc/unifi-poller/up.conf_.
*   **Instead of a config file, you may use environment variables.**
    Explained in the [Configuration](Configuration) wiki page.

## Docker Compose

Included with version 1.5.3 is a Docker Compose file and example environment variable
configuration to make it work. These were graciously provided [@jonbloom](https://github.com/jonbloom).
If you do not have Grafana or InfluxDB running already,
then this is a great option to let you try this software.

The docker compose files have been updated to work with poller v2.

*   Fill out the `docker-compose.env.example` file with the appropriate values
*   Save the file as `.env` in the same folder as your `docker-compose.yml` file.
*   Run `docker-compose up -d`

### Environment Variables

Example passing an env variable:

```shell
docker run -e UP_UNIFI_DEFAULT_PASS="your-secret-pasword" -e UP_POLLER_DEBUG="true" -d golift/unifi-poller:latest
```

#### Available Variables (v1)

For version 2.0 and beyond see the [Configuration](Configuration) doc.
Everything changed, and **the information below is only valid for versions 1.5.x and 1.6.x.**

|ENV v1.5|ENV v1.6|config|note|
|---|---|---|---|
UP_POLLING_MODE|UP_MODE|mode|`"influx"` (default), `"influxlambda"` or `"prometheus"`
UP_INFLUX_DB|UP_INFLUX_DB| influx_db | default `"unifi"`
UP_INFLUX_USER|UP_INFLUX_USER| influx_user| default `"unifi"`
UP_INFLUX_PASS|UP_INFLUX_PASS| influx_pass | default `"unifi"`
UP_INFLUX_URL|UP_INFLUX_URL| influx_url | default `"http://127.0.0.1:8086"`
UP_UNIFI_USER|UP_UNIFI_USER| unifi_user | default "influx"
UP_UNIFI_PASS|UP_UNIFI_PASS| unifi_pass |
UP_UNIFI_URL|UP_UNIFI_URL| unifi_url | default `"https://127.0.0.1:8443"`
UP_REAUTHENTICATE|UP_REAUTHENTICATE| reauthenticate | default `"false"`
UP_VERIFY_SSL|UP_VERIFY_SSL|verify_ssl|default `"false"`
UP_COLLECT_IDS|UP_SAVE_IDS|save_ids| default `"false"`
||UP_SAVE_SITES|save_sites| default `"true"`
UP_QUIET_MODE|UP_QUIET|quiet| default `"false"`
UP_DEBUG_MODE|UP_DEBUG|debug| default `"false"`
UP_POLLING_INTERVAL|UP_INTERVAL|interval|Go duration. ie `"1m"` or `"90s"`, default `"30s"`
UP_POLL_SITES|UP_SITES|sites|separate sites with commas, default `"all"`
||UP_NAMESPACE|namespace|top level Prometheus namespace. Default `"unifipoller"`
||UP_HTTP_LISTEN|http_listen|Prometheus HTTP listen address, `ip:port`. You will likely want to publish this port on the host.
