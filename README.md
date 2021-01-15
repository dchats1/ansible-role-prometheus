Role Name
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
prometheus_version                        | Version of Prometheus (Default: 2.24.0)
prometheus_base_dir                       | Directory to store prometheus configs (Default: /var/lib/containers)
prometheus_podman_network                 | Podman Network to deploy containers (Default: podman)
prometheus_domain                         | Domain name for prometheus containers (Default: example.com)
prometheus_hostname                       | Short hostname for containers (Default: prometheus)
prometheus_listen_port                    | Port for --web.listen-address (Default: 9090)
prometheus_ha_pair                        | Deploy Prometheus as HA pairs. (Default: false)
prometheus_additional_scrape_configs      | Additional scrape configs. See example in comments and down below. []

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
