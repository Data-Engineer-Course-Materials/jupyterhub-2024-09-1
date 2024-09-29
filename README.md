# jupyterhub-2024-09-1

## Install

1. Native (https://shakhbanov.org/kak-ustanovit-jupyterhub-na-vash-server-poshagovoe-rukovodstvo/)
2. Docker

Production-ready: 
- k8s cluster https://z2jh.jupyter.org/en/1.x/jupyterhub/index.html
- IaC terraform: https://github.com/HSPH-QBRC/jupyterhub-terraform/tree/master


## Uninstall

```shell
sudo apt-get remove jupyterhub
```

To remove jupyterhub configuration and data from Ubuntu we can use the following command:
```shell
sudo apt-get -y purge jupyterhub
```

## Dependencies
jupyterhub have the following dependencies:

- python3-alembic
- python3-async-generator
- python3-certipy
- python3-dateutil
- python3-entrypoints
- python3-jinja2
- python3-jupyter-telemetry
- python3-oauthlib
- python3-packaging
- python3-pamela
- python3-prometheus-client
- python3-requests
- python3-sqlalchemy
- python3-tornado
- python3-traitlets
- python3
