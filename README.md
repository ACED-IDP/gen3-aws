# gen3-aws
Documentation and utilities to accompany Gen3 AWS deployment

## Context 

see https://github.com/uc-cdis/cloud-automation/blob/master/doc/csoc-free-commons-steps.md


## Requirements

> In order to move on, you must have an EC2 instance up with an admin-like role attached to it. 

`EC2 instance` details:

* Before setting up the EC2 instance, create an IAM Role named "csoc_adminvm" with 
  * Attached policies
    * AdministratorAccess
  * Trust Relationship 
    * TODO - Overly Permissive policy Broad access: Principals that include a wildcard (*, ?) can be overly permissive.
    ```
    {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::280374789400:root"
        },
        "Action": "sts:AssumeRole",
        "Condition": {}
      },
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": "*"
        },
        "Action": "sts:AssumeRole",
        "Condition": {}
      }
    ]
    }
    ```

* Ubuntu 18 supported  ubuntu/images/hvm-ssd/ubuntu-bionic-18.04-amd64-server-20211129
* Assign the `IAM Role` to the csoc_adminvm role setup above

After launching, note the `VPC ID` you will need it.


![image](https://user-images.githubusercontent.com/47808/149028628-525e45d2-9d6e-44b9-b32f-a559898fe6ad.png)


## Before using


* Patch VM

> Install dependencies; you must run this part as a sudo access user.
> bash cloud-automation/gen3/bin/kube-setup-workvm.sh
You will need to expand the tempfs space

```
sudo mount -o remount,size=1G,noatime /run/user/1000
```

You will also need to patch `kube-setup-workvm.sh` see the `yq install`

```
sudo -E XDG_CACHE_HOME=/var/cache python3 -m pip install yq --ignore-installed PyYAML
```

## K8s setup

See https://github.com/uc-cdis/cloud-automation/blob/master/doc/csoc-free-commons-steps.md#third-part-deploy-the-kubernetes-cluster

> ec2_keyname an existing Key Pair in EC2 for the workers for deployment. More keys can be added automatically if you specify them in $HOME/cloud-automation/files/authorized_keys/ops_team.

![image](https://user-images.githubusercontent.com/47808/149221538-cf30e3a0-2b51-409b-947e-693811fa7d10.png)

To verify locally:

```
 ssh-keygen -ef aws-aced-bw.pem -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5
writing RSA key
(stdin)= 4f40f48d5e2ff685cdb285d803fd81c5
```

peering_vpc_id

### K8s scaling

The desired instances by default is `5` for testing, scale that back to 1 `$$$`

![image](https://user-images.githubusercontent.com/47808/149557076-5b996690-ba42-4bce-9658-48c42331ee5e.png)



## Domain

* Setup Route53 and certificate

 
You will need to set up a Route53 hosted zone, with certificate that points back to the ELB that terraform created

![image](https://user-images.githubusercontent.com/47808/149583022-d52e9741-e4ec-4804-b16d-c368b921aec1.png)

![image](https://user-images.githubusercontent.com/47808/149582851-f7ed45d4-a9a7-4444-af15-ecab56fa1bf8.png)



## Service configuration

Point of confusion:
  * You will need to edit `<environment>/apis_configs/fence-config.yaml` **not** the one in Gen3Secrets/apis_configs


## Post install steps

* You will need to bring up elastic search
  * `gen3 workon cdistest <commons-name>_es; gen3 tfplan ; gen3 tfapply`
  * `gen3 kube-setup-aws-es-proxy`
  
* Schedule an etl job
  * `gen3 runjob etl`

* And launch guppy
  * `gen3 gitops configmaps guppy; gen3 roll guppy`
  * see `gen3 gitops --help` for more

* Implement portal changes
  * `gen3 kube-setup-portal ; gen3 roll portal`
 

