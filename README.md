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

* Setup Route53 and certificate

![image](https://user-images.githubusercontent.com/47808/149029267-406011d8-b149-46c7-9ed5-14edeb78cd8d.png)


![image](https://user-images.githubusercontent.com/47808/149029468-45231a4d-f73d-42e0-b590-a4b7071f3530.png)




