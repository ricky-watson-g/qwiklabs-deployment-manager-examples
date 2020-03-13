# Cloud NAT

This example creates a Cloud NAT configuration within the Cloud Router configuration.  Each subnet requiring Cloud NAT needs a definition. Cloud NAT allows instances without public IP addresses to access internet resources.  

In this example the bastion can only be accessed via IAP because there is no public IP address.  The drawback with this approach is, what if you need to grab some files or update/install packages?  Cloud NAT will provide the internet connectivity.

[Cloud NAT](cloud_nat.jinja)
