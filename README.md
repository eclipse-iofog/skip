# Eclipse ioFog Platform

The Eclipse ioFog Platform project provides a means by which to spin up and deploy an Eclipse ioFog stack running
in the Cloud. Currently, we demonstrate how to achieve this on GKE and Packet, although since we are using 
[Terraform](https://www.terraform.io/) under the covers you can easily extend/contribute to support your preferred 
cloud infrastructure provider.

# Requirements

* GCloud SDK 
* Terraform (v0.11.x)
* ansible
* gcloud
* kubectl
* iofogctl

You can run `./bootstrap.sh` in order to download those dependencies.

## Option to deploy agent nodes on Packet
We support deployment of agent nodes on packet provided you have an account. In situations where you do not have your own devices acting as edge nodes, you can sping a few nodes on packet to act as agents. You will need Packet token to setup packet provider on terraform. Also be aware of account limitation for example,unable to spin more than 2 arm nodes per project. 
You will also need to make sure you have uploaded an ssh key on your packet project that will be used by automation to add to newly created instances. 
Uncomment out packet module and packet provider information if you are intending to use packet - ![main.tf](infrastructure/environments_gke/user/main.tf)

You will also require the following environment variables
```sh
export PACKET_AUTH_TOKEN=<packet_auth_token> # If you want to deploy agents on Packet

export GOOGLE_APPLICATION_CREDENTIALS=<path-to-json>
```
You can edit the file `./my_credentials.sh` to provide your keys.

## Usage

Run `./bootstrap.sh` to ensure all required dependencies are present and initialise terraform files.
If you didn't have `gcloud` prior to running the bootstrap script, please run:
```sh
  source /usr/local/lib/google-cloud-sdk/completion.bash.inc
  source /usr/local/lib/google-cloud-sdk/path.bash.inc
```

Edit the file `./my_vars.tfvars` according to the table below.

To deploy your ioFog stack, run `./deploy.sh`
To destroy your ioFog stack, run `./destroy.sh`

| Variables              | Description                                                  |
| -----------------------|:------------------------------------------------------------:|
| `project_id`           | *id of your google platform project*                         |
| `environment`          | *unique name for your environment*                           |
| `gcp_region`           | *region to spin up the resources*                            |
| `controller_image`     | *docker image link for controller setup*                     |
| `connector_image`      | *docker image link for connector setup*                      |
| `scheduler_image`      | *docker image link for scheduler setup*                      |
| `operator_image`       | *docker image link for operator setup*                       |
| `kubelet_image`        | *docker image link for kubelet setup*                        |
| `controller_ip`        | *list of edge ips, comma separated to install agent on*      |
| `ssh_key`              | *path to ssh key to be used for accessing edge nodes*        |
| `agent_repo`           | *use `dev` for snapshot repo, else leave empty*              |
| `agent_version`        | *populate if using dev snapshot repo for agent software*     |
| `packet_project_id`    | *packet project id to spin reposrces on packet*              |
| `operating_system`     | *operating system for edge nodes on packet*                  |
| `packet_facility`      | *facilities to use to drop agents*                           |
| `count_x86`            | *number of x86(make sure your project plan allow)*           |
| `plan_x86`             | *server plan for device on x86 available on facility chosen* |
| `count_arm`            | *number of arm sgents to spin up*                            |
| `plan_arm`             | *server plan for device on arm available on facility chosen* |
| `iofogUser_name`       | *name for registration with controller*                      |
| `iofogUser_surname`    | *surname for registration with controller*                   |
| `iofogUser_email`      | *email to use to register with controller*                   |
| `iofogUser_password`   | *password(length >=8) for user registeration with controller*|
| `iofogctl_namespace`   | *namespace to be used with iofogctl commands*                |
    
## iofogctl for Agent Configuration

If you plan to use snapshot repo, you will need to provide package cloud token, leave it empty if installing released version. 

### Helpful Commands

- Login to gcloud: `gcloud auth login`

- Kubeconfig for gke cluster: `gcloud container clusters get-credentials <<CLUSTER_NAME>> --region <<REGION>>`

- Delete a particular terraform resource: `terraform destroy -target=null_resource.iofog -var-file=vars.tfvars -auto-approve`

- Terraform Output `terraform output ` or `terraform output -module=packet_edge_nodes`