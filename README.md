# dev9 vault-consul-starter
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
aws_access_key_id = G4XAsdhjkASDEXAMPLE   
aws_secret_access_key = ghlfFhfdajkfFDAhfjkafEXAMPLE  

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

The first time you run this script it creates EC2 instances on AWS - sometimes there's an SSH glitch on the first run.  If you encounter this, just run the command again to see if it fixes it. (This is what I do).

To run only one tag add `--tags=TAGNAME`.

When running any of the above playbooks you must use either the  `--ask-vault-pass` or `--vault-password-file` command line options.  The first will prompt for the password, and the second will read a single line text file with the password, or output from a script that prints the password to standard out.

## Security group is wide open

This creates a wide open security group on EC2 for initial ease of use.  Lock down as needed.

## Vault needs to be started manually

Run: ansible-playbook -i ec2.py manual/system/start_service.yml -e 'keypair=FOO ansible_ssh_private_key_file=~/.ssh/FOO.pem' --extra-vars="groups=tag_Name_vault service_name=vault" -vvvv

For troubleshooting, you can also use the restart_service.yml in the same way as the above start_service.yml

## Initialize Vault

Run: ansible-playbook -i ec2.py manual/security/vault_init.yml -e 'keypair=FOO ansible_ssh_private_key_file=~/.ssh/FOO.pem' -vvvv

Record the output (contains critical passwords needed to operate Vault).  You need all 5 keys and the root key.

## Unseal Vault

Run: ansible-playbook -i ec2.py manual/security/vault_unseal.yml -e 'keypair=FOO ansible_ssh_private_key_file=~/.ssh/FOO.pem' -vvvv

Provide 3 of the 5 keys saved in the "Initialze Vault" step.

## (Optional) Setup Vault

Run: ansible-playbook -i ec2.py manual/security/vault_setup.yml -e 'keypair=FOO ansible_ssh_private_key_file=~/.ssh/FOO.pem' -vvvv

Installs different backends that you may or may not need.

## Check the HAProxy endpoint

http://amazonec2ipaddress:1936/

login/password: haproxy/haproxy

## Check Vault Seal Status (and other endpoints)

curl -k -H "X-Vault-Token: YOUR_ROOT_VAULT_TOKEN" https://amazonec2HAPROXYipaddress/v1/sys/seal-status

## Decrypt the secrets.yml file

The secrets.yml file stores passwords and other secure information.  Before running ansible you must
decrypt the file:

```bash
ansible-vault decrypt secrets.yml
```

Password is: vault

## Notes

ansible.cfg is provided and host key checking is set to False