# Learn Terraform Drift Management

This is a companion repository for the [Learn Terraform Drift Management tutorial](https://learn.hashicorp.com/tutorials/terraform/resource-drift) on HashiCorp Learn. Follow along to learn more about state management.

I. Create infrastructure

1. clone the repo
```
$ git clone https://github.com/krasteki/terraform-drift-management.git
```

2. Change into the repository directory.
```
$ cd learn-terraform-drift-management
```

3. Create an SSH key pair in your current directory, replacing your_email@example.com with your email address. Use an empty passphrase.
```
$ ssh-keygen -t rsa -C "your_email@example.com" -f ./key
```

4. Confirm your AWS CLI region.
```
$ aws configure get region
```

5. Open the `terraform.tfvars` file and edit the region to match the AWS CLI configuration.

6. `$ terraform init` and `$ terraform apply`

II. Introduce drift

1. To introduce a change to the configuration outside the Terraform workflow, create a new security group with the AWS CLI and export that value as an environment variable.
```
$  export SG_ID=$(aws ec2 create-security-group --group-name "sg_web" --description "allow 8080" --output text)
```
```
$ echo $SG_ID
```

2. create a new rule for the group to provide TCP access to the instance on port 8080.
```
$ aws ec2 authorize-security-group-ingress --group-name "sg_web" --protocol tcp --port 8080 --cidr 0.0.0.0/0
```

3. Associate the security group created manually with the EC2 instance provisioned by Terraform.
```
$ aws ec2 modify-instance-attribute --instance-id $(terraform output -raw instance_id) --groups $SG_ID
```

Now, the instance's SSH security group was replaced with a new security group that is not tracked in the Terraform state file.


III. Run a refresh-only plan

1. Run `terraform plan -refresh-only` to determine the drift between the current state file and actual configuration.
***Tip: The `-refresh-only` flag was introduced in Terraform 0.15.4, and is preferred over the `terraform refresh` subcommand.***

```
$ terraform plan -refresh-only
```

2. Apply these changes to make the state file match the real infrastructure, but not the Terraform configuration. Respond to the prompt with a `yes`
```
$ terraform apply -refresh-only
```

IV. Add the security group to configuration

1. Edit the `main.tf`. Add the created `aws_securitygroup` `sg_web`
2. Add the `aws_security_group_rule` `sg_web`
3. Add the security group ID to the instance resource 

V. Import the security group

1. Import the sec group
```
$ terraform import aws_security_group.sg_web $SG_ID
```

2. Import thr security group rule
```
$ terraform import aws_security_group_rule.sg_web "$SG_ID"_ingress_tcp_8080_8080_0.0.0.0/0
```

VI. Update the resources

1. Now that the `sg_web` security group is represented in state, re-run `terraform apply` to associate the SSH security group with your EC2 instance.
```
$ terraform apply
```

VII. Access the instance
```
$ ssh ubuntu@$(terraform output -raw public_ip) -i key
```
```
$ curl $(terraform output -raw public_ip):8080
```

VIII. Clean up the resources
```
$ terraform destroy
```