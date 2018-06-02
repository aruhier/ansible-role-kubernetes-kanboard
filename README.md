Ansible Role: kanboard for Kubernetes
===================================

Ansible role to install [kanboard](https://kanboard.org/) on Kubernetes.

Role Variables
--------------

```yaml
# Image used
kubernetes_kanboard_image: "kanboard/kanboard:latest"

# Namespace
kubernetes_kanboard_namespace: "default"
# App name (used as selector)
kubernetes_kanboard_app: "kanboard"
# Deployment name
kubernetes_kanboard_deployment: "kanboard-deployment"
# Service name
kubernetes_kanboard_service: "kanboard"

# Number of replicas
kubernetes_kanboard_replicas: 1
kubernetes_kanboard_revision_history: 1

# Node selector
kubernetes_kanboard_node_selector: {}

# Add custom labels in the deployment metadata section
kubernetes_kanboard_deployment_labels: {}
# Add custom annotations in the deployment metadata section
kubernetes_kanboard_deployment_annotations: {}

kubernetes_kanboard_resources:
  limits:
    memory: "512Mi"
  requests:
    memory: "64Mi"

# Setup ingress for kanboard
kubernetes_kanboard_setup_ingress: false
kubernetes_kanboard_ingress:
  name: "kanboard-ingress"
  host: "kanboard.example.com"
  annotations:
  tls:

# [database type]://[username]:[password]@[host]:[port]/ [database name]
# Example: postgres://foo:foo@myserver:5432/kanboard
kubernetes_kanboard_database_url: ""

### Volumes ###

# For each volume, `definition` and `subPath` can be used. Their content is
# exactly what would be in a Kkubernetes pod manifest.

# Data volume. Mounted in /var/www/app/data/ (see examples for more details)
kubernetes_kanboard_data_volume:
  definition:

# Plugins volume. Mounted in /var/www/app/plugins/ (see examples for more
# details)
kubernetes_kanboard_plugins_volume: {}
```

Dependencies
------------

Kubectl needs to be installed on the host targeted by the role.


Example Playbook
----------------

```yaml

- hosts: kube-master
  run_once: true
  vars:
    kubernetes_kanboard_data_volume:
      # This volume will be mounted as /var/www/app/data
      subPath: "data"
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: kanboard
          readOnly: false

    kubernetes_kanboard_plugins_volume:
      # This volume will be mounted as /var/www/app/plugins
      subPath: "plugins"
      definition:
        glusterfs:
          endpoints: gluster-example-cluster
          path: kanboard
          readOnly: false

    kubernetes_kanboard_setup_ingress: true
    kubernetes_kanboard_ingress:
      name: "kanboard-ingress"
      host: "kanboard.example.com"
      annotations:
        kubernetes.io/tls-acme: "true"
      tls:
        - secretName: "kanboard-ingress-tls"
          hosts:
            - "kanboard.example.com"
  roles:
    - role: Anthony25.kubernetes-kanboard
```

Use `run_once` to run the role on only one available master in the cluster.

License
-------

Tool under the BSD license. Do not hesitate to report bugs, ask me some
questions or do some pull request if you want to!
