Prometheus
=========

Deploy Prometheus in a podman container either as a single instance or as a HA Pair.
https://prometheus.io/

Requirements
------------

This requires the containers.podman collection: https://galaxy.ansible.com/containers/podman
Recommended 1.4.1 or later

Role Variables
--------------

Variable                                  | Description
------------------------------------------|--------------
prometheus_version                        | Version of Prometheus. (Default: 2.24.0)
prometheus_base_dir                       | Directory to store prometheus configs. (Default: /var/lib/containers)
prometheus_podman_network                 | Podman Network to deploy containers. (Default: podman)
prometheus_domain                         | Domain name for prometheus containers. (Default: example.com)
prometheus_hostname                       | Short hostname for containers. (Default: prometheus)
prometheus_listen_port                    | Port for --web.listen-address. (Default: 9090)
prometheus_ha_pair                        | Deploy Prometheus as HA pairs. (Default: false)
prometheus_flags                          | Flags to pass to Prometheus. See example below.
prometheus_thanos_sidecar                 | Deploy a Thanos sidecar. (Default: false)
prometheus_thanos_sidecar_flags           | Flags to pass to the Thanos sidecar. See example below.
thanos_version                            | Version of Thanos sidecar to deploy. (Default: 0.17.0)
prometheus_alerting_rules                 | Dictionary of names/urls for rules. See example below.
prometheus_additional_scrape_configs      | Additional scrape configs. See example in comments and down below.
prometheus_remote_write_configs           | Add remote_write configs. See example in comments and down below.

```
prometheus_flags:
  - '--web.listen-address=0.0.0.0:{{ prometheus_listen_port }}'
  - --config.file=/etc/prometheus/prometheus.yml
  - --storage.tsdb.path=/prometheus
  - --web.console.libraries=/usr/share/prometheus/console_libraries
  - --web.console.templates=/usr/share/prometheus/consoles
```

```
prometheus_thanos_sidecar_flags:
  - sidecar
  - --tsdb.path=/data
  - '--prometheus.url=http://localhost:{{ prometheus_listen_port }}'
  - |
    --objstore.config=type: S3 
    config:
      bucket: thanos
      region: us-east-1
      endpoint: s3.example.com
      access_key: secret
      secret_key: secret
      insecure: false
      signature_version2: false
```

```
prometheus_alerting_rules:
  - name: alertmanager
    url: https://raw.githubusercontent.com/monitoring-mixins/website/master/assets/alertmanager/alerts.yaml
  - name: prometheus
    url: https://raw.githubusercontent.com/monitoring-mixins/website/master/assets/prometheus/alerts.yaml
```

```
prometheus_additional_scrape_configs: |  
  - job_name: 'dns_sd_node'
   dns_sd_configs:
    - names:
      - _node._prometheus.example.com.
      refresh_interval: 30s
      type: SRV
  
  - job_name: 'grafana'
    static_configs:
      - targets:
        - grafana.example.com
    tls_config:
      ca_file: /etc/ssl/certs/example.pem
```
```
prometheus_remote_write_configs: |
  - url: http://cortex.example.com/api/prom/push
```

Dependencies
------------

No Dependencies at this time

Example Playbook
----------------

    - hosts: podman_host
      roles:
         - prometheus

License
-------

BSD

Author Information
------------------

David Chatterton
david@davidchatterton.com
