# Nomad and Consul Co-located Cluster Example

This folder shows an example of Terraform code to deploy a [Nomad](https://www.nomadproject.io/) cluster co-located 
with a [Consul](https://www.consul.io/) cluster in [Azure](https://azure.microsoft.com/) (if you want to run Nomad and Consul 
on separate clusters, see the [nomad-consul-separate-cluster example](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-separate-cluster) 
instead). The cluster consists of two Scale Sets: one with a small number of Nomad and Consul server 
nodes, which are responsible for being part of the [concensus 
protocol](https://www.nomadproject.io/docs/internals/consensus.html), and one with a larger number of Nomad and Consul 
client nodes, which are used to run jobs:

![Nomad architecture](https://raw.githubusercontent.com/hashicorp/terraform-azurerm-nomad/master/_docs/architecture-nomad-consul-colocated.png)

You will need to create an [Azure Managed Image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/build-image-with-packer) 
that has Nomad and Consul installed, which you can do using the [nomad-consul-ami example](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-ami)).  

For more info on how the Nomad cluster works, check out the [nomad-cluster](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/modules/nomad-cluster) documentation.

## Quick start

To deploy a Nomad Cluster:

1. `git clone` this repo to your computer.
1. Build a Nomad and Consul Azure Image. See the [nomad-consul-ami example](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-consul-ami) documentation for 
   instructions. Make sure to note down the URI of the Azure Image.
1. Install [Terraform](https://www.terraform.io/).
1. Open `vars.tf`, set the environment variables specified at the top of the file, and fill in any other variables that
   don't have a default, including putting your Azure Image ID into the `ami_id` variable.
1. Run `terraform init`.
1. Run `terraform plan`.
1. If the plan looks good, run `terraform apply`.
1. Run the [nomad-examples-helper.sh script](https://github.com/hashicorp/terraform-azurerm-nomad/tree/master/examples/nomad-examples-helper/nomad-examples-helper.sh) to print out 
   the IP addresses of the Nomad servers and some example commands you can run to interact with the cluster:
   `../nomad-examples-helper/nomad-examples-helper.sh`.
   
