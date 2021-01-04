# install openhab3 on raspberrypi

Add a file ```settings.yml``` to user-settings directory. See .dist file for help.
Replace your ip into the inventory file. 
## copy over your keys

e.g. for linux users: 
```ssh-copy-id -i ~/.ssh/mykey user@host```

## install ansible requirements

```bash
ansible-galaxy install -r requirements.yml
```

## samba share user/password

user: openhab
password: openhab

## start deployment

```ansible-playbook -i openhab openhab.yml```