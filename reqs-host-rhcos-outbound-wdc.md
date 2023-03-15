---

copyright:
  years: 2023, 2023
lastupdated: "2023-03-08"

keywords: satellite, requirements, outbound, network, allowlist, connectivity, firewall, rhcos

subcollection: satellite

---

{{site.data.keyword.attribute-definition-list}}

# RHCOS enabled locations in Washington D.C.
{: #reqs-host-rhcos-outbound-wdc}

The following network requirements are for outbound connectivity for Red Hat Enterprise Linux (RHEL) and Red Hat CoreOS (RHCOS) hosts for use with Red Hat CoreOS enabled locations in the Washington D.C. (`us-east`) region. 
{: shortdesc}

The type of location that you create dictates the type of operating systems that can run on your hosts. If your location is RHCOS enabled, then you can attach hosts that are running either RHEL and RHCOS. If your location isn't RHCOS enabled, then you can attach only hosts that are running RHEL. You can check whether your [location is RHCOS enabled](/docs/satellite?topic=satellite-locations#verify-coreos-location). For more information about operating system support, see [Planning your operating system](/docs/satellite?topic=satellite-infrastructure-plan#infras-plan-os).


You can verify your host setup with the `satellite-host-check` script. For more information, see [Checking your host setup](/docs/satellite?topic=satellite-host-network-check).
{: tip}


Review the following outbound network requirements for RHEL and RHCOS hosts for use with RHCOS enabled locations in the Washington D.C. (`us-east`) region.

Allow access to {{site.data.keyword.redhat_notm}} network time protocol (NTP) servers
:    * Destination hostnames: `0.rhel.pool.ntp.org`, `1.rhel.pool.ntp.org`, `2.rhel.pool.ntp.org`, `3.rhel.pool.ntp.org`
     * Protocol and ports: Allow NTP protocol and provide UDP on port 123
     
:    If you don't want to use {{site.data.keyword.redhat_notm}} network time protocol (NTP) servers, you can instead define a [custom NTP server for your RHCOS hosts](/docs/satellite?topic=satellite-config-custom-ntp).

Allow hosts to communicate with Red Hat Container Registry
:    Allow your hosts to access the required sites for OpenShift Container Platform. For more information, see [Configuring your firewall](https://docs.openshift.com/container-platform/4.8/installing/install_config/configuring-firewall.html){: external}.



Allow the following addresses, hostnames, protocols, and ports for {{site.data.keyword.satelliteshort}} control plane hosts in locations without CoreOS enabled.
:    * Destination IP addresses:  169.55.87.98, 169.63.138.34, 169.62.47.42 
     * Destination hostnames:  `c121.us-east.satellite.cloud.ibm.com`, `c121-1.us-east.satellite.cloud.ibm.com`, `c121-2.us-east.satellite.cloud.ibm.com`, `c121-3.us-east.satellite.cloud.ibm.com`, `c121-e.us-east.satellite.cloud.ibm.com` 
     * Protocol and ports: TCP 30000 - 32767

Allow hosts to be attached to a location and assigned to services in the location
:    * Destination IP addresses: 169.63.88.186, 169.60.73.142, 169.61.109.34
     * Destination hostnames: `origin.us-east.containers.cloud.ibm.com` 
     * Protocol and ports: HTTPS 443
     
Allow hosts to communicate with {{site.data.keyword.registrylong_notm}}
:    * Destination IP addresses: N/A
     * Destination hostnames: `icr.io`, `us.icr.io`, `registry.bluemix.net`, `registry.ng.bluemix.net`
     * Protocol and ports: HTTPS 443

Allow Link connectors to connect to the Link tunnel server endpoint
:    * Destination IP addresses: 169.47.156.154, 169.63.148.250, 169.62.1.34
     * Destination hostnames: `c-01-ws.us-east.link.satellite.cloud.ibm.com`, `api.link.satellite.cloud.ibm.com`
     * Protocol and ports: HTTPS 443

:    You can find the hostnames or IP addresses by running the `dig c-<XX>-ws.us-east.link.satellite.cloud.ibm.com +short` command. Replace `<XX>` with `01`, `02`, and so on, until no DNS results are returned.

Optional: Allow hosts to communicate with {{site.data.keyword.monitoringlong_notm}}
:    * Destination IP addresses and hostnames: [{{site.data.keyword.monitoringshort_notm}} endpoints](/docs/monitoring?topic=monitoring-endpoints)
     * Protocol and ports: HTTPS 443 and 6443

:    If you plan to use {{site.data.keyword.monitoringshort_notm}} in your {{site.data.keyword.openshiftshort}} {{site.data.keyword.satelliteshort}} clusters, then include these network options.
     
Optional: Allow hosts to communicate with {{site.data.keyword.loganalysislong_notm}}
:    * Destination IP addresses and hostnames: [{{site.data.keyword.loganalysislong_notm}} endpoints](/docs/log-analysis?topic=log-analysis-endpoints#endpoints_api_public)
     * Protocol and ports: HTTPS 443

:    If you plan to use {{site.data.keyword.loganalysislong_notm}} in your {{site.data.keyword.openshiftshort}} {{site.data.keyword.satelliteshort}} clusters, then include these network options.

Optional: Allow hosts to communicate with {{site.data.keyword.monitoringlong_notm}}
:    * Destination IP addresses and hostnames: [{{site.data.keyword.monitoringshort_notm}} endpoints](/docs/monitoring?topic=monitoring-endpoints)
     * Protocol and ports: HTTPS 443 and 6443

:    If you plan to use {{site.data.keyword.monitoringshort_notm}} in your {{site.data.keyword.openshiftshort}} {{site.data.keyword.satelliteshort}} clusters, then include these network options.