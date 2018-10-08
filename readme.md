Set of Ansible scripts to setup a Raspberry PI (stretch) into a kiosk mode.  
If you uncomfortable with Ansible, please refer to [text-instructions.md](./text-instructions.md)

### Installation

1. Add to `hosts.ini` domain name of a PI (for example: `1.local`)

2. In the folder `hosts_vars` create a file with a name of the domain (from a previous step) and put an ip address of the PI (on a local network)
    ```
    ansible_ssh_host: 192.168.1.36
    ```
    
3. Change the password hash and public keys to yours!

https://github.com/valiafetisov/raspberry-devops/blob/874675fe7826671b522fa2ef68a29162ad82af8c/00_initial_setup.yml#L8-L30

4. Run
    ```
    ansible-playbook -i hosts.ini --user pi --ask-pass -v 00_initial_setup.yml
    ```

