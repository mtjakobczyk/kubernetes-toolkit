### Provision Kubernetes on local VirtualBox VMs

1. Provision local VMs (VirtualBox) using Vagrant  
    Detailed information on Vagrant can be found [here](https://github.com/mtjakobczyk/linux-toolkit/blob/main/vms/README.md)
    ```
    cd ./1-infra-local/
    vagrant up
    ```
    There should be two local VirtualBox VMs launched (`control-1` and `worker-1`).

2. Provision Kubernetes using Ansible  
