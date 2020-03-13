# Cloud NAT

This example creates a Cloud NAT configuration within the Cloud Router configuration.  Each subnet requiring Cloud NAT needs a definition. Cloud NAT allows instances without public IP addresses to access internet resources.  

In this example the bastion can only be accessed via IAP because there is no public IP address.  The drawback with this approach is, what if you need to grab some files or update/install packages?  Cloud NAT will provide the internet connectivity.

You can replace the ROUTER_NAME and the NAT_NAME in this snippet below, and for each subnet, define an entry:

```
- name: {{ ROUTER_NAME }}
  type: compute.v1.router
  properties:
    network: $(ref.{{ NETWORK }}.selfLink)
    region: {{ REGION }}
    nats:
    - name: {{ NAT_NAME }}
      sourceSubnetworkIpRangesToNat: LIST_OF_SUBNETWORKS
      subnetworks:
      - name: $(ref.{{ SUBNET }}.selfLink)
        sourceIpRangesToNat:
        - PRIMARY_IP_RANGE
      natIpAllocateOption: AUTO_ONLY
```

[Back to TOC](../../README.md)
