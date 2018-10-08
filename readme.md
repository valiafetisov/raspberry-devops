Set of Ansible scripts to setup a Raspberry PI (stretch) into a kiosk mode.

### Installation

1. Add to `hosts.ini` domain name of a PI (for example: `1.local`)

2. In the folder `hosts_vars` create a file with a name of the domain (from a previous step) and put an ip address of the PI (on a local network)
    ```
    ansible_ssh_host: 192.168.1.36
    ```

3. Run
    ```
    ansible-playbook -i hosts.ini --user pi --ask-pass -v 00_initial_setup.yml
    ```

### Text instructions

If you uncomfortable with Ansible, please refer to [text-instructions.md](./text-instructions.md)
