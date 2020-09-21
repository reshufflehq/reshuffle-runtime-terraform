# Reshuffle Runtime Deployer

This [Terraform](https://www.terraform.io) script can be used to deploy
the [Reshuffle](https://reshuffle.com) runtime in your AWS account.

The script deploys the runtime docker image onto ECS Fargate, and configures
an HTTPS load-balancer to access the service. It assumes nothing about your
account and provisions the required network settings and IAM roles required
for the service to run.

## Prerequisits

1. AWS Account

1. A registered domain

1. SSL Certificate
([AWS Certificate Manager](https://aws.amazon.com/certificate-manager/)
will get you one for free)

1. AWS API credentials

## Usage

1. Set up your configuration in [terraform.tfvars](#vars)

1. Run `terraform init`

1. Run `terraform deploy`

1. Wait 30 seconds or so for the containers to spin up

1. Test you new service

## <a name="#vars"></a>Configuration in terraform.tfvars

Create a Terraform variables file `terraform.tfvars` with the following:

```
domain="example.com"
profile="<my-aws-credentials-profile>"
region="<aws-region>"
system="reshuffle"
```

These parameters describe your system and AWS environment. If you are using
AWS Route 53 as your DNS server on this domain, you can add

```
zoneid="<route-53-zone-id>"
```

This will allow Terraform to automatically set up a domain alias for
`<system>.<domain>` to point to the load blancer it configures. Given the
values above, this will expose you Reshuffle runtime at
`https://reshuffle.example.com`.

If you are not using Route 53, leave out `zoneid`. Terraform will print out
the load balancer domain name when it's done so you can configure the alias
yourself in your DNS server.

Finally, add the following parameters with the values received from your
Reshuffle contact:

```
studioAuthBaseURL="..."
studioBaseURL="..."
studioClientID="..."
studioClientSecret="..."
```

Make sure not to commit `terraform.tfvars` to git. It may contain secrets
and other confidential information.