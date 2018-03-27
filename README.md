# DevOps Kompose [![CircleCI](https://circleci.com/gh/int128/devops-kompose.svg?style=shield)](https://circleci.com/gh/int128/devops-kompose)

This is a compose of the following stack:

- Keycloak
- JIRA Software
- Grafana
- Prometheus

## Getting Started

### Prerequisite

If you want to create a Kubernetes cluster on AWS, see also [int128/kops-alb-starter](https://github.com/int128/kops-alb-starter).

Install the following tools:

```sh
brew install kubernetes-helm
curl -L -o ~/bin/helmfile https://github.com/roboll/helmfile/releases/download/v0.11/helmfile_darwin_amd64 && chmod +x ~/bin/helmfile
```

Set the environment specific vars in `.env`:

```sh
# .env
export DEVOPS_DOMAIN=devops.example.com
export DEVOPS_POSTGRES_HOST=xxx.xxx.us-west-2.rds.amazonaws.com
```

### Setup databases

This project assumes using the external PostgreSQL database such as RDS or Cloud SQL.

Create `.postgres-credentials` as follows:

```sh
# .postgres-credentials
host=xxxx.xxxx.us-west-2.rds.amazonaws.com
admin_user=foo
admin_password=foo
```

Create databases:

```sh
kubectl create namespace devops
kubectl create secret generic postgres-credentials -n devops --from-env-file .postgres-credentials
kubectl apply -f config/postgres-create-databases.yaml
```

### Install components

Create persistent volumes:

```sh
kubectl apply -f config/pvc.yaml
```

Install components:

```sh
helmfile sync
```

### Setup Keycloak

Create a new realm:

1. Open https://keycloak.example.com.
1. Create a new realm `devops`.
1. Create your user and set your initial password.
1. Create a new group `admin` and add you into the group.
1. Create a new role `admin` and assign all roles of the `realm-management` role.
1. Open https://keycloak.example.com/auth/admin/devops/console/.
1. Change your password.

### Setup JIRA Software

Setup your JIRA Software:

1. Open https://jira.example.com.
1. Configure the database.
1. Install a SAML or OpenID Connect plugin, e.g.:
    - [Jira Enterprise SSO with Keycloak](https://marketplace.atlassian.com/plugins/de.codecentric.atlassian.oidc.jira-oidc-plugin/server/overview)
    - [SAML 2.0 Single Sign-On for Jira](https://marketplace.atlassian.com/plugins/com.bitium.jira.SAML2PluginJira/server/overview) (free)

### Setup Grafana

1. Add the Prometheus data source to the Grafana.
  - Type: Prometheus
  - URL: http://prometheus-prometheus-server
  - Access: Proxy

## Contribution

This is an open source software licensed under Apache-2.0.
Feel free to open issues or pull requests.
