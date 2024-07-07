# File Browser

## Install

`curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash`

Default install in `/usr/local/bin`

## Config setting

### 
`cd /usr/local/bin`

### Generate config
`filebrowser -d filebrowser.db config init`

### Edit IP to 0.0.0.0, make externally accessible
`filebrowser -d filebrowser.db config set --address 0.0.0.0`

### Edit port (default: 8080)
`filebrowser -d filebrowser.db config set --port 8080`

### Edit language
`filebrowser -d filebrowser.db config set --locale zh-cn`

### Log
`filebrowser -d filebrowser.db config set --log /usr/local/bin/filebrowser.log`

### Set root directory (option)
>Don't set together with *WorkingDirectory*, or set a same directory

`filebrowser -d filebrowser.db config set --root /`

### Set user and password. --perm.admin set user be admin
`filebrowser -d filebrowser.db users add user password --perm.admin`

### Config info
`filebrowser -d filebrowser.db config cat`

### Config help
`filebrowser -d filebrowser.db config --help`

## System service

`sudo vim /etc/systemd/system/filebrowser.service`

### filebrowser.service

```
[Unit]
Description=filebrowser service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/filebrowser -d /usr/local/bin/filebrowser.db
WorkingDirectory=/
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

`sudo systemctl daemon-reload`

`sudo systemctl status filebrowser.service`

`sudo systemctl start filebrowser.service`

`sudo systemctl stop filebrowser.service`

`sudo systemctl enable filebrowser.service`

`sudo systemctl restart filebrowser.service`
