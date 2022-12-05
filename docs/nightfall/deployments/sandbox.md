---
id: sandbox
title: Run Nightfall on a Sandboxed Environment
sidebar_label: Sandbox
description: "To run a simple cloud deployment"
keywords:
  - docs
  - polygon
  - nightfall
  - sandbox
  - address
  - aws
image: https://matic.network/banners/matic-network-16x9.png
---

## Tools

Running Nightfall on your own "sandboxed" environment means you'll have a running ganache network, together with an optimist, a worker, a client, and any other services you need to start working on your Nightfall integration.

The instructions in this guide assume a deployment on AWS, although you should be able to mimic them for any other cloud service provider (see the relevant section below). We will deploy a single EC2 instance running the `./start-nightfall` script while exposing the ports needed to interact with it.

## What you'll need

### Terraform

This guide leverages [Terraform](https://www.terraform.io/) as it allows easy, reusable infrastructure-as-code that can expand to suit your needs. To install Terraform, follow the instructions [here](https://www.terraform.io/downloads).

### AWS account

If you don't have an AWS account, you should create one [here](https://aws.amazon.com) and follow the instructions [here](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html) on how to get credentials, AKA `access_key` and `secret_key`. Please note them, as we will be using them for our deployment.

### SSH keypair (optional)

You need a keypair if you want to access your EC2 instance via SSH. You may do so by following the instructions [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html#having-ec2-create-your-key-pair).

## Infrastructure

### AWS

First, create a new directory you will use for Terraform and enter it:

`mkdir nightfall && cd nightfall`

And start adding files. For simplicity's sake, let's use one simple file: `nightfall.tf`.

And add the AWS resources:

```tf title="nightfall.tf"
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region     = "eu-west-3"
  access_key = // YOUR_ACCESS_KEY
  secret_key = // YOUR_SECRET_KEY
}
```

This allows Terraform to use your AWS credentials to deploy and destroy resources.

#### EC2

Next, let's add the resources themselves. Mind that we're using a keypair called `ssh`. If you don't want to access the instance via `ssh`, remove the line `key_name = "ssh"` from the script below.

```tf title="nightfall.tf"
resource "aws_security_group" "main" {
  egress = [
    {
      cidr_blocks      = [ "0.0.0.0/0", ]
      description      = ""
      from_port        = 0
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "-1"
      security_groups  = []
      self             = false
      to_port          = 0
    }
  ]
 ingress                = [
   {
     cidr_blocks      = [ "0.0.0.0/0", ]
     description      = ""
     from_port        = 22
     ipv6_cidr_blocks = []
     prefix_list_ids  = []
     protocol         = "tcp"
     security_groups  = []
     self             = false
     to_port          = 22
   },
   {
     cidr_blocks      = [ "0.0.0.0/0", ]
     description      = ""
     from_port        = 8080
     ipv6_cidr_blocks = []
     prefix_list_ids  = []
     protocol         = "tcp"
     security_groups  = []
     self             = false
     to_port          = 8081
    },
    {
      cidr_blocks      = [ "0.0.0.0/0", ]
      description      = ""
      from_port        = 8092
      ipv6_cidr_blocks = []
      prefix_list_ids  = []
      protocol         = "tcp"
      security_groups  = []
      self             = false
      to_port          = 8092
    }
  ]
}

resource "aws_instance" "nightfall" {
  ami           = "ami-064736ff8301af3ee"
  instance_type = "t3.large"
  user_data = "${file("nightfall.sh")}"
  key_name = "ssh"  # Remove if not needed!
  vpc_security_group_ids = [aws_security_group.main.id]
  depends_on = [ aws_security_group.main ]

  root_block_device {
    volume_size = 29
  }
}

```

This opens all ports for outgoing traffic. It also opens `ssh` ports for incoming traffic and ports `8080`, `8081`, and `8092`, which will be used for Nightfall.

Finally, for convenience's sake, you also want a nice output of your instance's public IP, so you will want to add the following:

```tf title="nightfall.tf"
output "instance_dns" {
  description = "The public ip"
  value       = aws_instance.nightfall.public_dns
}
```

#### Nightfall scripts

We also need to add a script that will install and run Nightfall automatically when your EC2 instance starts. Just copy this content into a file named `nightfall.sh`:

```bash title="nightfall.sh"
#!/bin/bash

## Installing Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker

## Installing docker-compose
curl -SL https://github.com/docker/compose/releases/download/v2.12.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

## Installing nvm
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash 
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm

## Cloning Nightfall repo and installing the NPM version used
git clone https://github.com/EYBlockchain/nightfall_3.git
cd nightfall_3
nvm install

## Building the containers
./bin/setup-nightfall
## Starting core nightfall services (worker, client, optimist)
./bin/start-nightfall -g -d &> nightfall-core.log &disown

## Waiting for deployer to finish
while ! docker wait nightfall_3-deployer-1; do sleep 3; done
## Starting nightfall apps
./bin/start-apps &> nightfall-apps.log &disown

```

:::note
Notice the last two lines, which are meant to start a proposer. **If you don't want to run a proposer, remove these lines.**
:::

### Checklist

Now, you should have at least two files in your directory:
- `nightfall.tf` with the terraform resources
- `nightfall.sh` with the nightfall script
- Optionally, a ssh certificate. In this example, it's called `ssh.pem`


### Deploy

We're all set. Just run `terraform init` to start the new project and `terraform apply` to run through the resources Terraform will create for you. Write "yes" if you agree, and wait several minutes for the script to run.

The IP address of your EC2 instance will show up. Copy it for later.
If you want to see the whole process running, access your instance with `ssh -i ssh.pem ubuntu@YOUR-INSTANCE-PUBLIC-IP` and `tail -f /var/log/cloud-init-output.log`.

#### Access

You should be able to access the services using the IP address you have from the previous step.

- On port 8080, you have a nightfall client you can connect to with `nightfall-sdk`, `postman`, etc. 
- On 8081, you'll find the optimist. 
- On 8092 you'll reach the proposer.

### Other cloud providers

The above script will work on any remote machine running Ubuntu. If you don't use AWS, you can run this script on any remote machine your preferred cloud provider runs. We don't provide a handy Terraform script to deploy these EC2 instances.

