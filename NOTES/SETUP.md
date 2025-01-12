
# Create Virtual Machine

For the purpose of our setup we created a Virtual Machine in our Development cluster following this procedure:
* https://confluence.egt-digital.com/display/K8S/How+to+create+a+Virtual+Machine

The machine was with the following resouces:
RAM: 32GB
CPUs: 12
Disk size: 200 GB

Image: Ubuntu 22.04 LTS
Network: dev-network

# Setup
I follow the instructions from official documentation for setup:
* https://docs.victoriametrics.com/quick-start/#starting-vm-single-from-a-binary

1. Download needed tar gz from official repository and check the sha256 hash sums;
2. Create service user for the process;
3. Create a directory for storage;
4. Create a systemd service;
5. Reload systemd daemon and 'enable --now' the service;

We also have an Ansible role which do all of that:
https://github.com/VictoriaMetrics/ansible-playbooks
