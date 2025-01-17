---

copyright:
  years: 2023, 2023
lastupdated: "2023-06-12"

keywords: satellite, requirements, outbound, network, allowlist, connectivity, firewall, rhcos

subcollection: satellite

---

{{site.data.keyword.attribute-definition-list}}


# RHCOS enabled locations with reduced firewall in Frankfurt
{: #req-minimum-outbound-fra}

Review the following network requirements for outbound connectivity for hosts in a minimum internet access location in the Frankfurt (`eu-de`) region. Because this type of location requires a single network destination instead of multiple destinations, it reduces the number of outbound IP addresses that you must allow from your firewall. For more information, see [Creating Red Hat CoreOS enabled Locations with reduced firewall footprint](/docs/satellite?topic=satellite-coreos-reduced-firewall).
{: shortdesc}


You can verify your host setup with the `satellite-host-check` script. For more information, see [Checking your host setup](/docs/satellite?topic=satellite-host-network-check).
{: tip}

  
The following outbound network requirements are specific for hosts in the Frankfurt (`eu-de`) region.

Allow Link connectors to connect to the Link tunnel server endpoint
:    * Destination IP addresses: 149.81.188.130, 158.177.75.210, 161.156.38.2  
     * Destination hostnames:  `c-01-ws.eu-de.link.satellite.cloud.ibm.com`
     * Protocol and ports: HTTPS 443


Allow access to {{site.data.keyword.redhat_notm}} network time protocol (NTP) servers
:    * Destination hostnames: `0.rhel.pool.ntp.org`, `1.rhel.pool.ntp.org`, `2.rhel.pool.ntp.org`, `3.rhel.pool.ntp.org`
     * Protocol and ports: Allow NTP protocol and provide UDP on port 123
     
:    If you don't want to use {{site.data.keyword.redhat_notm}} network time protocol (NTP) servers, you can instead define a [custom NTP server for your RHCOS hosts](/docs/satellite?topic=satellite-config-custom-ntp).

Optional:  Allow hosts to connect to HPCS for encrypting cluster secrets.
:    * Domain: `api.eu-de.hs-crypto.cloud.ibm.com`
     * Port: 8000-19999 

:    If you have a preconfigured set of instances, you can find the assigned port to your instance in the overview page and allowlist just that port on the domain.

For access to services such as {{site.data.keyword.loganalysislong_notm}} or {{site.data.keyword.monitoringlong_notm}}, you must add additional outbound access. For more information, see [RHCOS enabled locations in Frankfurt](/docs/satellite?topic=satellite-reqs-host-rhcos-outbound-fra).
  
 


