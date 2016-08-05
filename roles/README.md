# SSL Certificate Playbook

## Overview

For a Host needing to provide an SSL Certificate, this will create the self-signed certificates.
The certificate is copied to Ansible host for later deployment to clients. Certificate should be
checked into version control.<p>

Clients needing access to this server over SSL and thus this certificate will need to copy the saved
certificate to an appropriate location. The certificate will be under the `files/ssl/<host_ip>/etc/ssl`
directory on the Ansible host.

## SSL Config

The ssl config file that is generated provides for multiple host names and ip addresses such as those
found in an AWS VPC with public/private ip address/hostname combinations.
