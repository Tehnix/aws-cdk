## AWS::GlobalAccelerator Construct Library
<!--BEGIN STABILITY BANNER-->
---

![cfn-resources: Stable](https://img.shields.io/badge/cfn--resources-stable-success.svg?style=for-the-badge)

> All classes with the `Cfn` prefix in this module ([CFN Resources](https://docs.aws.amazon.com/cdk/latest/guide/constructs.html#constructs_lib)) are always stable and safe to use.

![cdk-constructs: Experimental](https://img.shields.io/badge/cdk--constructs-experimental-important.svg?style=for-the-badge)

> The APIs of higher level constructs in this module are experimental and under active development. They are subject to non-backward compatible changes or removal in any future version. These are not subject to the [Semantic Versioning](https://semver.org/) model and breaking changes will be announced in the release notes. This means that while you may use them, you may need to update your source code when upgrading to a newer version of this package.

---
<!--END STABILITY BANNER-->

## Introduction

AWS Global Accelerator is a service that improves the availability and performance of your applications with local or global users. It provides static IP addresses that act as a fixed entry point to your application endpoints in a single or multiple AWS Regions, such as your Application Load Balancers, Network Load Balancers or Amazon EC2 instances.

This module supports features under [AWS Global Accelerator](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_GlobalAccelerator.html) that allows users set up resources using the `@aws-cdk/aws-globalaccelerator` module. 

## Accelerator

The `Accelerator` resource is a Global Accelerator resource type that contains information about how you create an accelerator. An accelerator includes one or more listeners that process inbound connections and direct traffic to one or more endpoint groups, each of which includes endpoints, such as Application Load Balancers, Network Load Balancers, and Amazon EC2 instances. 

To create the `Accelerator`:

```ts
import globalaccelerator = require('@aws-cdk/aws-globalaccelerator');

new globalaccelerator.Accelerator(stack, 'Accelerator');

```

## Listener

The `Listener` resource is a Global Accelerator resource type that contains information about how you create a listener to process inbound connections from clients to an accelerator. Connections arrive to assigned static IP addresses on a port, port range, or list of port ranges that you specify. 

To create the `Listener` listening on TCP 80:

```ts
new globalaccelerator.Listener(stack, 'Listener', {
  accelerator,
  portRanges: [
    {
      fromPort: 80,
      toPort: 80,
    },
  ],
});
```


## EndpointGroup

The `EndpointGroup` resource is a Global Accelerator resource type that contains information about how you create an endpoint group for the specified listener. An endpoint group is a collection of endpoints in one AWS Region. 

To create the `EndpointGroup`:

```ts
new globalaccelerator.EndpointGroup(stack, 'Group', { listener });

```

## Add Endpoint into EndpointGroup

You may use the following methods to add endpoints into the `EndpointGroup`:

- `addEndpoint` to add a generic `endpoint` into the `EndpointGroup`.
- `addLoadBalancer` to add an Application Load Balancer or Network Load Balancer.
- `addEc2Instance` to add an EC2 Instance.
- `addElasticIpAddress` to add an Elastic IP Address.


```ts
const endpointGroup = new globalaccelerator.EndpointGroup(stack, 'Group', { listener });
const alb = new elbv2.ApplicationLoadBalancer(stack, 'ALB', { vpc, internetFacing: true  });
const nlb = new elbv2.NetworkLoadBalancer(stack, 'NLB', { vpc, internetFacing: true });
const eip = new ec2.CfnEIP(stack, 'ElasticIpAddress');
const instances = new Array<ec2.Instance>();

for ( let i = 0; i < 2; i++) {
  instances.push(new ec2.Instance(stack, `Instance${i}`, {
    vpc,
    machineImage: new ec2.AmazonLinuxImage(),
    instanceType: new ec2.InstanceType('t3.small'),
  }));
}

endpointGroup.addLoadBalancer('AlbEndpoint', alb);
endpointGroup.addLoadBalancer('NlbEndpoint', nlb);
endpointGroup.addElasticIpAddress('EipEndpoint', eip);
endpointGroup.addEc2Instance('InstanceEndpoint', instances[0]);
endpointGroup.addEndpoint('InstanceEndpoint2', instances[1].instanceId);
```
