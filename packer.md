# How to Build Custom AWS AMIs with Packer

## Packer

[Packer](https://developer.hashicorp.com/packer/docs/intro) is an open source tool for creating identical machine images for multiple platforms from a single source configuration. Packer is lightweight, runs on every major operating system, and is highly performant, creating machine images for multiple platforms in parallel.

A *machine image* is a single static unit that contains a pre-configured operating system and installed software which is used to quickly create new running machines. An example of this Amazon Machine Image.

Build an Image with Packer

1. Install Packer

```bash
# Windows
choco install packer


```
With Packer installed, it is time to build your first image. In this tutorial, you will build a `t2.micro` Amazon EC2 AMI

## Packer template

A Packer template is a configuration file that defines the image you want to build and how to build it. Packer templates use the Hashicorp Configuration Language (HCL).

```bash
# Make a directory

mkdir packer_tutorial

cd packer_tutorial
```

Create a file called `aws-ubuntu.pkr.hcl`, add the following HCL block to it, and save the file

```bash
packer {
  required_plugins {
    amazon = {
      version = ">= 0.0.2"
      source  = "github.com/hashicorp/amazon"
    }
  }
}

source "amazon-ebs" "ubuntu" {
  ami_name      = "learn-packer-linux-aws"
  instance_type = "t2.micro"
  region        = "us-west-2"
  source_ami_filter {
    filters = {
      name                = "ubuntu/images/*ubuntu-xenial-16.04-amd64-server-*"
      root-device-type    = "ebs"
      virtualization-type = "hvm"
    }
    most_recent = true
    owners      = ["099720109477"]
  }
  ssh_username = "ubuntu"
}

build {
  name    = "learn-packer"
  sources = [
    "source.amazon-ebs.ubuntu"
  ]
}

```

> Make sure you have the required infrastructure VPC internet gateway