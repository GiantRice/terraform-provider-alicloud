---
layout: "alicloud"
page_title: "Alicloud: alicloud_slb"
sidebar_current: "docs-alicloud-resource-slb"
description: |-
  Provides an Application Load Banlancer resource.
---

# alicloud\_slb

Provides an Application Load Balancer resource.

-> **NOTE:** Resource `alicloud_slb` has deprecated 'listener' filed from terraform-alicloud-provider [version 1.3.0](https://github.com/alibaba/terraform-provider/releases/tag/V1.3.0) . You can create new listeners for Load Balancer by resource `alicloud_slb_listener`.
If you have had several listeners in one load balancer, you can import them via the specified listener ID. In the `alicloud_slb_listener`, listener ID is consist of load balancer ID and frontend port, and its format is `<load balancer ID>:<frontend port>`, like "lb-hr2fwnf32t:8080".

-> **NOTE:** At present, to avoid some unnecessary regulation confusion, SLB can not support alicloud international account to create "paybybandwidth" instance.

-> **NOTE:** The supported specifications vary by region. Currently not all regions support guaranteed-performance instances.
For more details about guaranteed-performance instance, see [Guaranteed-performance instances](https://www.alibabacloud.com/help/doc-detail/27657.htm).

## Example Usage

```
# Create a new load balancer for classic
resource "alicloud_slb" "classic" {
  name                 = "test-slb-tf"
  internet             = true
  internet_charge_type = "PayByBandwidth"
  bandwidth            = 5
  specification = "slb.s1.small"
}

# Create a new load balancer for VPC
resource "alicloud_vpc" "default" {
  # Other parameters...
}

resource "alicloud_vswitch" "default" {
  # Other parameters...
}

resource "alicloud_slb" "vpc" {
  name       = "test-slb-tf"
  vswitch_id = "${alicloud_vswitch.default.id}"
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Optional) The name of the SLB. This name must be unique within your AliCloud account, can have a maximum of 80 characters,
must contain only alphanumeric characters or hyphens, such as "-","/",".","_", and must not begin or end with a hyphen. If not specified,
Terraform will autogenerate a name beginning with `tf-lb`.
* `internet` - (Optional, Forces New Resource) If true, the SLB addressType will be internet, false will be intranet, Default is false. If load balancer launched in VPC, this value must be "false".
* `internet_charge_type` - (Optional, Forces New Resource) Valid
  values are `PayByBandwidth`, `PayByTraffic`. If this value is "PayByBandwidth", then argument "internet" must be "true". Default is "PayByTraffic". If load balancer launched in VPC, this value must be "PayByTraffic".
  Before version 1.10.1, the valid values are "paybybandwidth" and "paybytraffic".
* `bandwidth` - (Optional) Valid
  value is between 1 and 1000, If argument "internet_charge_type" is "paybytraffic", then this value will be ignore.
* `listener` - (Deprecated) The field has been deprecated from terraform-alicloud-provider [version 1.3.0](https://github.com/alibaba/terraform-provider/releases/tag/V1.3.0), and use resource `alicloud_slb_listener` to replace.
* `vswitch_id` - (Required for a VPC SLB, Forces New Resource) The VSwitch ID to launch in.
* `specification` - (Optional) The specification of the Server Load Balancer instance. Default to empty string indicating it is "Shared-Performance" instance.
 Launching "[Performance-guaranteed](https://www.alibabacloud.com/help/doc-detail/27657.htm)" instance, it is must be specified and it valid values are: "slb.s1.small", "slb.s2.small", "slb.s2.medium",
 "slb.s3.small", "slb.s3.medium" and "slb.s3.large".
* `tags` - (Optional) A mapping of tags to assign to the resource. The `tags` can have a maximum of 10 tag for every load balancer instance.

~> **NOTE:** A "Shared-Performance" instance can be changed to "Performance-guaranteed", but the change is irreversible.

~> **NOTE:** To change a "Shared-Performance" instance to a "Performance-guaranteed" instance, the SLB will have a short probability of business interruption (10 seconds-30 seconds). Advise to change it during the business downturn, or migrate business to other SLB Instances by using GSLB before changing.

## Attributes Reference

The following attributes are exported:

* `id` - The ID of the load balancer.
* `name` - The name of the load balancer.
* `internet` - The internet of the load balancer.
* `internet_charge_type` - The internet_charge_type of the load balancer.
* `bandwidth` - The bandwidth of the load balancer.
* `vswitch_id` - The VSwitch ID of the load balancer. Only available on SLB launched in a VPC.
* `address` - The IP address of the load balancer.
* `specification` - The specification of the Server Load Balancer instance.

## Import

Load balancer can be imported using the id, e.g.

```
$ terraform import alicloud_slb.example lb-abc123456
```