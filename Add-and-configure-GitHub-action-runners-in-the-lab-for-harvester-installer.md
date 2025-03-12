- Provision a bare metal machine in the lab
    - Leap 15.6 on NVME/SSD, 1T space. Assume the user is `harvester`
    - Use UEFI mode and enable virtualization in the BIOS
- Some pre-configuration
    
    ```bash
    # passwordless sudo
    sudo -i
    echo "harvester ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/harvester
    chmod 440 /etc/sudoers.d/harvester
    
    # for ansible
    zypper in python311 python311-requests
    
    # not recommended in public cloud
    systemctl disable --now firewalld
    
    reboot
    ```
    
- Clone https://github.com/bk201/harvester-ci and configure the server
    
    ```bash
    # ensure you can ssh into the runner with key first
    
    git clone https://github.com/bk201/harvester-ci
    cd harvester-ci
    
    # edit inventory and add the machine
    cd ansible
    vim inventory/hosts
    
    # change the host in the playbook
    vim node-ci-opensuse.yaml
    
    # configure the server
    ansible-playbook node-ci-opensuse.yaml
    ```
    
    The machine should be good for living as a runner.

- Add the runner to the repo
    - Visit https://github.com/harvester/harvester-installer/settings/actions/runners and add a x86 64 runner
    - Log in to the runner
        
        ```bash
        sudo su - github
        
        # follow the github's guide to download and configure runner. 
        # https://github.com/harvester/harvester-installer/settings/actions/runners/new?arch=x64&os=linux
        # Adding these labels
        kvm,vagrant,equinix
        
        # configure the runner service
        exit
        sudo su -
        cd /home/github/actions-runner/
        ./svc.sh install github
        
        # enable and start the service (change the unit name accordingly)
        systemctl enable --now actions.runner.harvester-harvester-installer.<runner-name>.service
        
        ```
     

