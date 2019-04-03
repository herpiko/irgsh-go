# irgsh-go

IRGSH (https://groups.google.com/d/msg/blankon-dev/yvceclWjSw8/HZUL_m6-BS4J, pronunciation: *irgis*) is an all-in-one tool to create and maintain Debian-derived GNU/Linux distribution: from packaging to repository, from ISO build to release management. This codebase is a complete rewrite of the old IRGSH components (https://github.com/BlankOn?q=irgsh).

## Requirements

```
sudo apt-get install -y pbuilder debootstrap devscripts python-apt reprepro
```

## Install

Before install, make sure you stopped all the running irgsh-\* instances. To install or update irgsh-go, you can use the install script using cURL:

```
curl -o- https://raw.githubusercontent.com/BlankOn/irgsh-go/master/utils/scripts/install.sh | bash
```

## The Instances

Minimal IRGSH ecosystem contains three instances that supposed to be live on different machines. They depends on Redis as backend (queue, pubsub).

- `irgsh-chief` acts as the master. The others (also applied to`irgsh-cli`) will talks to the chief.
- `irgsh-builder` is the builder worker of IRGSH.
- `irgsh-repo` will serve as repository so it may need huge volume of storage.

We may need more than one `irgsh-builder`, depends on our available resources. Please refer to `/etc/irgsh/config.yml` for available preferences. Before going to run any of these, you need to prepare your GPG key for signing purpose and set it into `config.yml` (see `GPG-EN.md`).

Running the chief is quite simple as starting the service with `/etc/init.d/irgsh-chief start`, as well for `irgsh-builder` and `irgsh-repo`. For `irgsh-builder` and `irgsh-repo`, we need to initialize them first. Please note that the initialization command should be running under root.

Initialize the builder to create and prepare pbuilder,

```
irgsh-builder init
```

Initialize the repo to create and prepare reprepro repository,

```
irgsh-repo init

```

After these three instances are up and running, you may continue to work with `irgsh-cli` from anywhere.

## CLI

`irgsh-cli` need to be initialized first to define the `irgsh-chief` instance address,

```
irgsh-cli --chief http://irgsh.blankonlinux.or.id:8080 init
```

Then you can submit a package,

```
irgsh-cli --source https://github.com/BlankOn/bromo-theme.git --package https://github.com/BlankOn-packages/bromo-theme.git submit
```

And checking the status of a pipeline,

```
irgsh-cli --pipeline 2019-04-01-174135_1ddbb9fe-0517-4cb0-9096-640f17532cf9 status
```


## Todos

### CLI

- Submit :heavy_check_mark:
- GPG signing
- Live logging via WebSocket

### Chief

- Auth (GPG or mutual auth)
- WebSocket
- Builder registration
- Repo registration

### Builder

- Init:
  - base.tgz :heavy_check_mark:
- Clone :heavy_check_mark:
- Signing :heavy_check_mark:
- Build :heavy_check_mark:
- Upload :heavy_check_mark:
- Dockerized pbuilder

### Repo

- Init :heavy_check_mark:
- Sync :heavy_check_mark:
- Download :heavy_check_mark:
- Inject :heavy_check_mark:
- Rebuild repo :heavy_check_mark:

### PabrikCD

- Build
- Upload

### Others

- Daemonized instances :heavy_check_mark:
- No sudo needed :heavy_check_mark:
- Secure Redis connection
