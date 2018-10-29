These templates can be used to install a Cloudify Manager in a public cloud.

# AWS

##### Installing a Cloudify Manager using the CloudFormation Template from the AWS Console:
1. Navigate to the CloudFormation [console](https://console.aws.amazon.com/cloudformation).
1. Select "Create Stack".
1. Under "Choose a template", select "Specify an Amazon S3 template URL" and insert the URL of this [template](https://s3.amazonaws.com/cfy-cfn-templates/aws.yaml).
1. Click "Next" to arrive at the "Specify Details" page.
1. Provide a "Stack Name", such as "Cloudify".
1. Fill in values for the parameters. You must provide a value for "InstancesKeyName". This should be the name of a KeyPair in your AWS Account/Region.
1. Click "Next" to arrive at the "Options" page.
1. Scroll to the bottom and click "Next" again to arrive at the "Review" page.
1. Click "Create" to create the CloudFormation stack.
1. When the stack has a status "CREATE_COMPLETE", select the "Outputs" tab and copy the "URL" output. This is your Cloudify Manager's public IP address.

##### Configuring the Cloudify Manager using the Cloudify Manager UI:
1. Paste your Cloudify Manager public IP address in navigation bar of your web browser.
1. You will be redirected to `http://your-ip-address/console/login`. Login with the default username and password, `admin`.
1. On the left side menu, select the `System Resources` page. You will arrive at `http://your-ip-address/console/page/system_resources`.

###### Creating Secrets:
1. Scroll to the `Secret Store Management` widget.
1. Select the `+Create` button to create secrets. Create secrets for each of these secret keys, providing values in the `secret value` field:
    + `aws_access_key_id`: This is your AWS access key ID. For more info, see [here](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys).
    + `aws_secret_access_key`. This is your AWS secret access key. For more info, see [here](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys).
1. Create also a secret for your private key pair. This provide the value in the `Get secret value from file (max 50kB)` field:
    + `agent_key_private`: This is the path to a public key file on your workstation, for example `~/.ssh/id_rsa`.
    + `agent_key_public`: This is the path to a public key file on your workstation, for example `~/.ssh/id_rsa.pub`.

###### Uploading Plugins:
1. Scroll to the `Plugins` widget.
1. Select the `^Upload`  button to upload plugins. You will need to upload both the `YAML` and the `Wagon`. You can get the URLs of the latest plugin releases [here](https://cloudify.co/plugins).

###### Allow access to non-local networks:

When a Cloudify Manager creates a virtual machine, it will install an agent by default. This agent communicates with the manager on its `broker IP address`. You can control which IP it will contact by providing an `agent network`. You can add such IPs using the following steps.

_This operation must be performed via a shell on the Cloudify Manager._
1. SSH into your Cloudify Manager. `ssh -i ~/.ssh/id_rsa centos@your-ip-address`.
1. Execute the following commands, replacing `your-ip-address` with your Cloudify Manager's IP:
    + `cfy_manager add-networks --networks '{"external": "your-ip-address"}'`
    + `sudo systemctl restart nginx`
    + `sudo systemctl restart cloudify-mgmtworker`
    + `sudo systemctl restart cloudify-rabbitmq`
