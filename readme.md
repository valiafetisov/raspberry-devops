For text instructions please refer to [text-instructions.md](./text-instructions.md)

### Настройка

1. Добавить в `hosts.ini` доменное имя пирожка (например: `1.local`)

2. В папке `hosts_vars` создать файл с именем домена и в нем указать ip адрес
    ```
    ansible_ssh_host: 192.168.1.36
    ```

3. Выполнить
    ```
    ansible-playbook -i hosts.ini -v 00_initial_setup.yml --user pi --ask-pass --ask-sudo-pass
    ```
