# Internal Load Balancer Terraform Module
Modular Internal Load Balancer for GCE using forwarding rules.

### Load Balancer Types
* [TCP load balancer](https://github.com/terraform-google-modules/terraform-google-lb)
* [HTTP/S load balancer](https://github.com/terraform-google-modules/terraform-google-lb-http)
* **Internal load balancer**

## Compatibility
This module is meant for use with Terraform 0.13+ and tested using Terraform 1.0+. If you find incompatibilities using Terraform >=0.13, please open an issue.
 If you haven't
[upgraded](https://www.terraform.io/upgrade-guides/0-13.html) and need a Terraform
0.12.x-compatible version of this module, the last released version
intended for Terraform 0.12.x is [v2.3.0](https://registry.terraform.io/modules/terraform-google-modules/-lb-internal/google/v2.3.0).

## Upgrading

The current version is 2.X. The following guides are available to assist with upgrades:

- [1.X -> 2.0](./docs/upgrading_to_lb_internal_v2.0.md)

## Usage

```hcl
module "gce-ilb" {
  source       = "GoogleCloudPlatform/lb-internal/google"
  version      = "~> 2.0"
  region       = var.region
  name         = "group2-ilb"
  ports        = ["80"]
  health_check = var.health_check
  source_tags  = ["allow-group1"]
  target_tags  = ["allow-group2", "allow-group3"]
  backends     = [
    { group = module.mig2.instance_group, description = "", failover = false },
    { group = module.mig3.instance_group, description = "", failover = false },
  ]
}
```


## Resources created

- [`google_compute_forwarding_rule.default`](https://www.terraform.io/docs/providers/google/r/compute_forwarding_rule): The internal regional forwarding rule.
- [`google_compute_region_backend_service.default`](https://www.terraform.io/docs/providers/google/r/compute_region_backend_service): The backend service registered to the given `instance_group`.
- [`google_compute_health_check.tcp`](https://www.terraform.io/docs/providers/google/r/compute_health_check): The TCP health check for the `instance_group` targets.
- [`google_compute_health_check.http`](https://www.terraform.io/docs/providers/google/r/compute_health_check): The HTTP health check for the `instance_group` targets.
- [`google_compute_health_check.https`](https://www.terraform.io/docs/providers/google/r/compute_health_check): The HTTPS health check for the `instance_group` targets.
- [`google_compute_firewall.default-ilb-fw`](https://www.terraform.io/docs/providers/google/r/compute_firewall): Firewall rule that allows traffic from the `source_tags` resources to `target_tags` on the `service_port`.
- [`google_compute_firewall.default-hc`](https://www.terraform.io/docs/providers/google/r/compute_firewall): Firewall rule that allows traffic for health checks to the `target_tags` resources.
