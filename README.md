# minecraft-server
Minecraft server for CS 312 Course Project Part 2

## Background

This sets up a Minecraft server on AWS EC2. Terraform creates the EC2 instance and security group, Ansible installs Java and sets up the Minecraft server as a systemd service so it restarts automatically on boot.

## Requirements

You need [Terraform](https://developer.hashicorp.com/terraform/install) 1.15+, [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) 2.16+, [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) 2.x, and nmap installed.

Get your AWS credentials from the Learner Lab by clicking "AWS Details", then run aws configure and enter your keys. Also run aws configure set aws_session_token YOUR_TOKEN.

Download the vockey PEM file from the Learner Lab, save it to ~/vockey.pem, and run chmod 600 ~/vockey.pem.

## Pipeline

local machine -> Terraform -> AWS EC2 instance + security group -> Ansible -> Java + Minecraft server + systemd service

## Commands

Initialize the infrastructure:

```bash
terraform -chdir=terraform init
terraform -chdir=terraform apply -var="key_name=vockey"
```
Note the public IP from the output and update ansible/inventory.ini with it:

```bash
nano ansible/inventory.ini
```

Then configure the server:

```bash
ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
```

If the service is not running after the playbook finishes:

```bash
ssh -i ~/vockey.pem ubuntu@YOUR_IP "sudo systemctl daemon-reload && sudo systemctl restart minecraft"
```

## Connecting

Verify the server is running with nmap:

```bash
nmap -sV -Pn -p T:25565 {your ip}
```


## Resources

- https://developer.hashicorp.com/terraform/docs
- https://docs.ansible.com
- https://www.minecraft.net/en-us/download/server
- https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
