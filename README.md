# dev9-vault-consul
Ansible deploy of clustered Vault, Consul and HAProxy instance on AWS EC2

## Requirements

* Python 2.6+
* Pip
* Ansible (pip install ansible)
* Boto (pip install boto)
* Six (pip install six)
* httplib2 (pip install httplib2)

## AWS Credentials

Boto, the AWS library used by Ansible, needs your AWS credentials.

Export `AWS_ACCESS_KEY` and `AWS_SECRET_KEY` as environment
variables before running the playbook. These are your AWS access values.

```bash
export AWS_ACCESS_KEY_ID="G4XAsdhjkASDEXAMPLE"
export AWS_SECRET_ACCESS_KEY="ghlfFhfdajkfFDAhfjkafEXAMPLE"
```

## Add aws credentials

Place this in the file ~/.aws/credentials

[Credentials]  
aws_access_key_id = <your_access_key_here>  
aws_secret_access_key = <your_secret_key_here>  

## Add ec2.ini environment variable

The ec2.py script needs a configuration file named ec2.ini

Add this environment variable:

```bash
export EC2_INI_PATH="~/<directory>/ec2.ini"
```

where ~/<directory> is the path to your ec2.ini

## Usage

To install Vault, Consul and HAProxy on AWS EC2 run:

    ansible-playbook -i ec2.py env.yml -e 'keypair=FOO ansible_ssh_private_key_file=~/.ssh/FOO.pem'
    
where FOO is the name of your [AWS keypair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html).

To run only one tag add `--tags=TAGNAME`.

When running any of the above playbooks you must use either the  `--ask-vault-pass` or `--vault-password-file` command line options.  The first will prompt for the password, and the second will read a single line text file with the password, or output from a script that prints the password to standard out.

## Decrypt the secrets.yml file

The secrets.yml file stores passwords and other secure information.  Before running ansible you must
decrypt the file:

```bash
ansible-vault decrypt secrets.yml
```

Password is: vault
