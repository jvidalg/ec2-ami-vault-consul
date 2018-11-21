# Vault and Consul AMI - Packer

This repo is a customized version from the Hashicorp official module [vault-consul-ami](https://github.com/hashicorp/terraform-aws-vault/tree/master/examples/vault-consul-ami) from this Module and the [install-consul](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-consul) and [install-dnsmasq](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/install-dnsmasq) modules
from the Consul AWS Module with [Packer](https://www.packer.io/) to create [Amazon Machine Images
(AMIs)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) that have Vault and Consul installed on top of:

1. Ubuntu 16.04
1. Amazon Linux

You can use this AMI to deploy a [Vault cluster](https://www.vaultproject.io/) by using the [vault-cluster
module](https://github.com/hashicorp/terraform-aws-vault/tree/master/modules/vault-cluster). This Vault cluster will use Consul as its storage backend, so you can also use the
same AMI to deploy a separate [Consul server cluster](https://www.consul.io/) by using the [consul-cluster
module](https://github.com/hashicorp/terraform-aws-consul/tree/master/modules/consul-cluster).




## Steps to build AWS AMIs


1. `git clone` this repo to your computer.

1. Install [Packer](https://www.packer.io/).

1. Configure your AWS credentials using one of the [options supported by the AWS SDK](http://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/credentials.html). Usually, the easiest option is to
   set the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables.
    ###### Create environment variables
    ```bash
    export AWS_ACCESS_KEY_ID = ""
    export AWS_SECRET_ACCESS_KEY = ""
    ```
    ###### Create credentials file
    ```bash
    mkdir $HOME/.aws
    echo "[default]
    aws_access_key_id=
    aws_secret_access_key= " >> $HOME/.aws/credentials
    ```
    if you are using multiple AWS Profiles, make sure you create your AMIs in the desired one using the following env variables, otherwise they will be created in the default profile:

    ```bash
    export AWS_PROFILE=profilename
    export AWS_DEFAULT_PROFILE=profilename
    ```


1. In this repo we are using the TLS certificates that come with the official template due to this is only for demo purposes. If you are planning to use it in a production environment or another managing sensitive data, you must use the [private-tls-cert module](https://github.com/hashicorp/terraform-aws-vault/tree/master/modules/private-tls-cert)
   TLS cert:


1. Update the `variables` section of the `vault-consul-ami.json` Packer template to specify the:
 AWS region
 Vault version
 Consul version
 Paths to the TLS cert files you just generated.
 If you want to install Consul Enterprise, Vault Enterprise or obtain the files from a specific location no matter if Enterprise or not, skip the version variables and instead set the `consul_download_url` and `vault_download_url` to the full urls that point to the respective enterprise zipped packages or your on-premises location.

  Example:

  ```bash
   export VAULT_DOWNLOAD_URL="https://releases.hashicorp.com/vault/0.11.5/vault_0.11.5_linux_amd64.zip"
   export CONSUL_DOWNLOAD_URL="https://releases.hashicorp.com/consul/1.3.1/consul_1.3.1_linux_amd64.zip"
   ```

1. Run `packer build vault-consul.json`.
