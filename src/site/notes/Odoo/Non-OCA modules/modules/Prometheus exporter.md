---
{"dg-publish":true,"topics":"Odoo modules","permalink":"/odoo/non-oca-modules/modules/prometheus-exporter/","dgPassFrontmatter":true}
---


![](https://www.odoo-wiki.org/assets/icon_oms_box-61bea3f9.png)

Odoo metrics with Prometheus monitor.

Technical name: `prometheus_exporter`  
Repository: [https://github.com/Mint system/Odoo-Apps-Server-Tools/tree/14.0/prometheus-exporteropen in new window](https://github.com/Mint-System/Odoo-Apps-Server-Tools/tree/14.0/prometheus_exporter)

## Use

### Create Odoo metric

Navigate by _Settings > Technical > Metrics_ and create a new entry.

TIP

By default, the ana number of entries are published as a metric value as a measurement value as a date of date model and filter.

![](https://www.odoo-wiki.org/assets/prometheus-exporter-metrics-details-d61ae8ff.png)

### Create Odoo metric with operation

First, you need to [create](https://www.odoo-wiki.org/prometheus-exporter.html#odoo-metrik-erstellen) a [Odoo metric](https://www.odoo-wiki.org/prometheus-exporter.html#odoo-metrik-erstellen) and then select a field in the optional _Measured Field_ field. If a field has been selected, the operation option appears_Operation_. Here you can select the calculation method.

![](https://www.odoo-wiki.org/assets/prometheus-exporter-measured-field-85306a4a.png)

### Get Odoo metrics

The Odoo metrics are included in `/metrics`provided. Call the metric page with the Url [https://example.com/metricsopen in new window](https://example.com/metrics).

![](https://www.odoo-wiki.org/assets/prometheus-exporter-metrics-d8d2edf3.png)
