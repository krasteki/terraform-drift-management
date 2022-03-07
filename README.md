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