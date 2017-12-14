AWS ECS Worker Service Terraform module
========================

Terraform module which create an ECS worker service into an ECS cluster passed as an argument

Worker intend to be an ECS service publishing no ports, an example can be a monitoring agent running as an ECS service sending data to an monitoring DB.

Usage
-----

```hcl
data "template_file" "container_defs" {
  template = "${file("container_definitions.json")}"

  vars {
    name     = "worker"
    env      = "testing"
    memory   = "128"
    cpu      = "1"
  }
}

module "ecs-service" {
  source                = "git::https://github.com/egarbi/terraform-aws-ecs-worker"
  name            = "example"
  environment     = "testing"
  desired_count   = "1"
  cluster         = "example-cluster"
  iam_role        = "arn:aws:iam::123203969087:role/ec2_role"
  container_definitions = "${data.template_file.container_defs.rendered}"
}
```
The referenced `container_definitions.json` file contains a valid JSON document, which is shown below, and its content is going to be passed directly into the container_definitions attribute as a string. Please note that this example contains only a small subset of the available parameters.
```
[
  {
    "name": "${name}",
    "image": "service-first",
    "cpu": "${cpu}",
    "memory": ${memory},
    "essential": true,
    "environment": [
      {
        "name": "SERVICE_NAME",
        "value": "${name}"
      }
    ]
  }
]

```
