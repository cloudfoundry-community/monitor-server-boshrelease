BOSH release for monitor-server
===============================

Bonus monit scripts to watch your BOSH VMs and raise alerts. Include in any existing BOSH deployment for bonus monitoring!

![pagerduty](http://cl.ly/image/0g2c2m3B0w45/photo.jpg)

Includes

-	ephemeral disk alerts (`/var/vcap/data` volume)
-	persistent disk alerts (`/var/vcap/store` volume)

Usage
-----

To each of your BOSH deployments, add the following:

```yaml
releases:
- name: monitor-server
  version: latest
...
```

In each job that includes a persistent disk, add the `monitor-server` job template:

```yaml
jobs:
- name: postgres
  templates:
  - name: postgres
    release: postgres
  - name: monitor-server
    release: monitor-server
```

See section Configuration for configuration options and the defaults.

To use this BOSH release, first upload it to your BOSH:

```
bosh upload release https://bosh.io/d/github.com/cloudfoundry-community/monitor-server-boshrelease
```

Finally, run `bosh deploy` for your modified, existing BOSH deployment manifest.

The jobs with `monitor-server` template included will now generate alerts via BOSH Health Monitor if they fill up.

Setup BOSH to send alerts
-------------------------

The documentation for BOSH shows how to configure your Micro BOSH or BOSH to send notifications to email/SMTP, Pager Duty, Data Dog, etc:

-	https://bosh.io/docs/monitoring.html
-	https://bosh.io/docs/hm-config.html
-	https://bosh.io/docs/vm-monit.html

Configuration
-------------

You can configure all jobs or specific jobs with different alert limits.

You can configure all jobs simultaneously using global properties in your manifest:

```yaml
properties:
  monitor_server:
    ephemeral_disk:
      alert_percent: 80
    persistent_disk:
      alert_percent: 80
```

Each job that includes the `monitor-server` job template can individually configured with alert limits that override the overrides above. Each deployment manifest job can have its own properties section:

```yaml
jobs:
- name: postgres
  templates:
  - name: postgres
    release: postgres
  - name: monitor-server
    release: monitor-server
  properties:
    monitor_server:
      ephemeral_disk:
        alert_percent: 80
      persistent_disk:
        alert_percent: 80
```
