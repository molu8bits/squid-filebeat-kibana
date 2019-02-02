# squid-filebeat-kibana
Filebeat module for Squid access logs + Kibana dashboards. ELK 6.5

# TL;DR<p>
Collect your squid access.log with Filebeat, send directly to Elasticsearch.
Get overview of Squid access log using Kibana dashboard.

![](_images/kibana_dashboard_example.png)


# Elastichsearch and Kibana
1. Elasticsearch and Kibana
    <p>a.)Install Elasticsearch and Kibana.
    <p>b.) Configure firewall to allow access from filebeat host to elasticsearch service.
    <p>c.) enable plugins "ingest-geoip":
    ```bash
    bin/elasticsearch-plugin install ingest-geoip"
    ```


# Filebeat + module squid installation
2. Configuration Filebeat (6.5 recommended. 5.x should work as well)
   <p>a.) copy filebeat/module/squid into /usr/share/filebeat/module
   <p>b.) copy filebeat/etc/filebeat/modules.d/squid.yml.disabled into /etc/filebeat/modules.d
   <p>c.) configure /etc/filebeat/filebeat.yml - reference file placed in /etc/filebeat/filebeat.yml
        (change  hosts ["elasticsearch.local"] in section output.elastichsearch to elastichsarch instance listening from filebeat host
   <p>d.) enable Filebeat squid module by command "filebeat modules enable squid" (or just rename /etc/filebeat/modules.d/squid.yml.disabled to /etc/filename/modules.d/squid.yml
   <p>e.) restart Filebeat service - "systemctl restart filebeat"

# Kibana configuration
3. Import object into Kibana (via GUI: Management -> Saved Objects -> import) using following order
   (Dashboard requires the visualisations and the search object - they must be imported successfully first)
   <p>a.) 01_visualisations_All.json
   <p>b.) 02_search_Squid-Proxy-Access.json
   <p>c.) 03_Filebeat-Squid-Dashboard.json

# Check Dashboard view on Kibana
4. Go to the Dashboard section and find "Filebeat-Squid-Dashboard". Set Time-Range according logs from Squid



# Troubleshooting
<p>Elasticsearch needs to know what types should be applied to particular fields during processing logs.
For squid they exist in filebeat/module/squid/access/_meta/fields.yml.
If they are not applied automatically on the Filebeat Index (and default, incorrect mapping exists)
then apply following procedure to enforce them once again:
Add content of "filebeat/etc/squid-fields.yml" to /etc/filebeat/fields.yml, remove existing pipleline
then restart filebeat service e.g.

```bash
cat filebeat/etc/squid-fields.yml >> /etc/filebeat/fields.yml
curl -XDELETE elasticsearch.local:9200/filebeat-index-name
curl -XDELETE elasticsearch.local:9200/_ingest/pipeline/filebeat*squid*
systemctl restart filebeat
```
