# tf_aws_puppet

Terraform module for pre-baked [puppet](https://puppetlabs.com/) masters and agents using
[external nodes](https://docs.puppetlabs.com/guides/external_nodes.html)
driven by Tags on the EC2 instances

## puppetmaster

### Inputs

  * region - The region this puppetmaster is being launched in, e.g. eu-central-1
  * instance_type - The type of instance to use
  * iam_instance_profile - An IAM Role which can do the DescribeInstances call - used by the ENC to look up tags
  * aws_key_name - A key for administrating the machine
  * subnet_id - The subnet to launch the machine in
  * security_group - The security group to launch the machine in
  * repository - The repository to clone/update on the puppet master

### Outputs

  * private_ip - the IP of the new puppet master

### Example

    module "puppetmaster" {
      source = "github.com/terraform-community-modules/tf_aws_puppet/master"
      region = "${var.region}"
      instance_type = "t2.micro"
      iam_instance_profile = "describe-instances"
      aws_key_name = "${var.admin_key}"
      subnet_id = "${var.subnet_id}"
      security_group = "${var.security_group_allow_all}"
      repository = "https://github.com/bobtfish/terraform-puppet.git"
    }

## puppet client

### Inputs

  * region - The region this puppetmaster is being launched in, e.g. eu-central-1
  * instance_type - The type of instance to use
  * iam_instance_profile - An IAM Role which can do the DescribeInstances call - used by the ENC to look up tags
  * aws_key_name - A key for administrating the machine
  * subnet_id - The subnet to launch the machine in
  * security_group - The security group to launch the machine in
  * puppet_role - The 'puppet_role' tag to give your instance. This should correspond to modules/role/manifests/NAME.pp in your puppet repository!

### Outputs

  * private_ip - the IP of the new puppet client

### Example

    module "puppet-client" {
      source = "github.com/terraform-community-modules/tf_aws_puppet/agent"
      region = "${var.region}"
      instance_type = "t2.micro"
      iam_instance_profile = "describe-instances"
      aws_key_name = "${module.vpc.aws_key_name}"
      subnet_id = "${element(split(\",\", module.vpc.dedicatedsubnets), 0)}"
      security_group = "${module.vpc.security_group_allow_all}"
      puppetmaster_ip = "${module.puppetmaster.private_ip}"
      puppet_role = "unknown"
    }

# LICENSE

Apache2 - See the included LICENSE file for more information.

