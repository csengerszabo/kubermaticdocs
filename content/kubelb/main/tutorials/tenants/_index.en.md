+++
title = "Tenants"
linkTitle = "Tenants"
date = 2023-10-27T10:07:15+02:00
weight = 2
+++

Tenants represent the consumers of the load balancer services in the management cluster. They can be individual users, teams, or applications that have their workloads, access control, and quotas isolated by using the tenant concept in management cluster. Tenants are represented by the tenant CRD and have a dedicated namespace `tenant-<tenant-name>` in the management cluster.

## Kubermatic Kubernetes Platform (Enterprise Edition Only)

For details, go through [KKP integration details]({{< relref "../../tutorials/kkp">}})

## Usage

For usage outside of KKP please follow the guide along. This guide assumes that the KubeLB manager cluster has been configured by following the [installation guide](../../installation/).

### KubeLB Manager configuration

With KubeLB v1.1, the process to register a new tenant has been simplified. Instead of running scripts to register a new tenant, the user can now create a `Tenant` CRD.

```yaml
apiVersion: kubelb.k8c.io/v1alpha1
kind: Tenant
metadata:
  name: shroud
spec:
  propagatedAnnotations: null
  # Propagate all annotations to the resources.
  propagateAllAnnotations: true
  loadBalancer:
    class: "metallb.universe.tf/metallb"
    # Enterprise Edition Only
    limit: 10
  ingress:
    class: "nginx"
  gatewayAPI:
    class: "eg"
  # All of the below configurations are Enterprise Edition Only
  dns:
    allowedDomains:
      - "*.example.com"
  certificates:
    defaultClusterIssuer: "letsencrypt-prod"
    allowedDomains:
      - "*.example.com"
  allowedDomains:
    # All subdomains of example.com are allowed but at a single lower level. For example, kube.example.com, test.example.com, etc.
    - "*.example.com"
    # All subdomains of kube.com are allowed but at any lower level. For example, example.kube.com, test.tenant1.prod.kube.com etc.
    - "**.kube.com"
```

With this CR we are creating a tenant named `shroud` with the following configurations:

* **propagateAllAnnotations: true** - Propagate all annotations to the resources.
* **loadBalancer.class: metallb.universe.tf/metallb** - The class to use for LoadBalancer resources for tenants in the management cluster.
* **loadBalancer.limit: 10** - The limit of LoadBalancer resources that can be created by the tenant.
* **ingress.class: nginx** - The class to use for Ingress resources for tenants in the management cluster.
* **gatewayAPI.class: eg** - The class to use for Gateway API resources for tenants in the management cluster.
* For DNS configuration, we have allowed domains `*.example.com`.
* For Certificates configuration, we have the default cluster issuer `letsencrypt-prod` and allowed domains `*.example.com`.
* For Ingress and Gateway API, we have allowed domains `*.example.com` and `**.kube.com`.

**For more details and options, please go through [CRD References]({{< relref "../../references">}})**