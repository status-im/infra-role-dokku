# Description

This is a deployment of [Dokku](https://github.com/dokku/dokku), a docker-powered PaaS that helps you build and manage the lifecycle of applications.

# Configuration

In order to configure apps use the `dokku_apps` variable:

```yaml
dokku_apps:
  - name: 'my-awesome-bot'
    repo: 'https://github.com/status-im/my-awesome-bot'
    env:
      APP_ID: 12345
      LOG_LEVEL: debug
      WEBHOOK_SECRET: 'my-github-webhook-secret'
      PRIVATE_KEY: 'my-secret-private-key'
```

# Known Issues

## Docker Container Renaming

Because we redirect logs to `rsyslog` and then to files in `/var/log/docker` as well as Logstash, and the way Docker handles container renaming, when Dokku renames containers their logs still flow to the randomly generated name, rather then the propper app name.

For this reason a hacky workaround has been added to the [`tasks/post_config.yml`](tasks/post_config.yml) file which modifies Dokku script files.

For more details see: https://github.com/docker/for-linux/issues/582

## Docker User Namespaces

Currently Dokku is unable to properly work with Docker user namespace settings.

For that reason we are using a hacky workaround in [`tasks/dockremap.yml`](tasks/dockremap.yml) which simply changes the user in Docker `userns-remap` setting to `dokku` rather than `dockremap`.

This allows us to use to the user namespaces without getting `chmod` errors on container start.

For more details see: https://github.com/dokku/dokku/issues/3454
