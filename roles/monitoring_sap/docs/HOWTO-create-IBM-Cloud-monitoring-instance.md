# Create an IBM Cloud Monitoring Instance with your IBM IAM account and extract paremeters "url" and "credentials"

One IBM Cloud monitoring instance collects and supplies visualized SAP monitoring Dashboards
for several SAP Systems delivered by a monitoring host in the same region using private ingestion endpoints.
We recommend monitoring SAP Systems in different regions with using IBM Cloud monitoring instances in each corresponding region.
You could reach one single IBM Cloud monitoring instance from every region or even from outside IBM Cloud with using public URL endpoints.

In IBM Cloud Web UI navigate to
Observability/Monitoring: click the button ‘create +’
(as described in  [IBM Cloud Doku](https://cloud.ibm.com/docs/monitoring?topic=monitoring-getting-started#getting-started-step2)):

- Select location: the monitoring instance must be in the same region as the monitoring host
- Select the resource group
- Select the pricing plan "Graduated Tier"
  (as lite plan does not allow prometheus calls)
- Service name: the name is arbitrary
- IBM platform metrics: Disable

### Enabling VRF and service endpoints in the IBM Cloud IAM account

Enabled VRF and service endpoints are the default settings in the IAM account.
In case creating a monitoring instance is impossible, enable these setting in your IAM account,
which is described in the [IBM cloud document](https://cloud.ibm.com/docs/account?topic=account-vrf-service-endpoint&interface=ui).

Only the owner of the IAM account can change these settings, not an authorized member of
the IAM account.

### Extract the URL and credentials for prometheus access to the IBM Cloud monitoring instance

The URL and credentials are required to authenticate access to the monitoring instance.
Navigate in the IBM Cloud UI:
Observability/Monitoring: choose your monitoring instance, "Open dashboard"

* click "Get Started" on the bottom left panel
* click "Get Connected"
* click "Optional: Connect your Prometheus Servers"
* copy url and authorization:credentials

example result in the UI:

```
remote_write:
- url: "https://ingest.prws.us-south.monitoring.cloud.ibm.com/prometheus/remote/write"
  authorization:
    credentials: "d92XXXX-f8XX-4fXX-a6XX-34XXX"
```

Use the whole URL as value of the variable <ibmcloud_monitoring_instance_url> in the monitoring configuration.

All private ingestion endpoints per region are listed in [IBM Cloud Document](https://cloud.ibm.com/docs/monitoring?topic=monitoring-prometheus_remote_write#prometheus_remote_write_endpoints)


Use the credentials as value of the variable <ibmcloud_monitoring_authorization_credentials>.
