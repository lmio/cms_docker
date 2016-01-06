Making AMIs for Contest Management System
-----------------------------------------

These scripts make it easy to do the following:

1. Create deb file of Contest Management System.
2. Create an AMI using that debian file.

About the setup
---------------

We are using a fork of cms 1.2 with a few additions. See [the
branch](https://github.com/lmio/cms/tree/lmio2015) for the code changes.

We assume the following setup in AWS:

1. **worker** node running cmsWorkers only. Runs on c4.xlarge or larger
   (otherwise it's impossible to turn off hyperthreading). Uses
`/usr/local/etc/cms_worker.conf`.
2. **cws** node running two CWS instances on ports 9000 (contest id=1) and 9001
   (contest id=2). Uses `/usr/local/etc/cms_main.conf`.
3. **centriukas** running everything else. It also runs nginx on port 80 http
   basic-auth. Hostname: `centriukas.cms1`. Uses
`/usr/local/etc/cms_main.conf`.
4. **Database**. User: `cmsdb`. Hostname: `cmsdb.cms1`.

Getting started
---------------

Requirements:

1. Machine with Docker 1.9+ for building the deb file. If in EC2, you don't
   need the second one.
2. EC2 machine with [aminator](https://github.com/Netflix/aminator) for
   creating the AMI.

Installing recent Docker on Ubuntu 14.04:

```bash
sudo apt-key adv \
    --keyserver hkp://p80.pool.sks-keyservers.net:80 \
    --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
echo 'deb http://apt.dockerproject.org/repo ubuntu-trusty main' | \
    sudo tee /etc/apt/sources.list.d/docker.list
sudo apt-get update
sudo apt-get install docker-engine
```

EC2 machine policy
------------------

1. Create a new policy:

```javascript
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt0123456789012",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CopySnapshot",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DeregisterImage",
        "ec2:DetachVolume",
        "ec2:RegisterImage",
        "ec2:Describe*"
      ],
      "Effect": "Allow",
      "Resource": [
        "*"
      ]
    }
  ]
}
```

2. Create a new role for *Amazon EC2*: *Allows EC2 instances to call AWS
   services on your behalf.*

3. When creating the EC2 instance, select the newly created role.

Creating the deb image
----------------------

```
make deb ADMINPW=admin_passwd DBPASSWD=db_passwd
```

The image will be in `build/cms_aws.deb`.

Creating the AMI
----------------

TBD. Will be:

```
make ami
```