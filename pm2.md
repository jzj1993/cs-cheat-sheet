# PM2



## Install

```bash
sudo npm install -g pm2
# or
sudo yarn global add pm2
```



## Manage Node App

```bash
# run `npm dev` with pm2 using name `myapp`
pm2 start npm --name myapp -- run dev

# stop/restart/log
pm2 stop/restart/log myapp
```



## Show App Status

```bash
pm2 list/status
```



## Get PM2 Status

PM2 create one daemon for each user by default.

> Share daemon between multiple users: https://medium.com/@sobus.piotr/pm2-share-the-same-daemon-process-between-multiple-users-dd7ecae6197a

```bash
# check pm2 daemon
ps aux | grep PM2
# root  1015  0.2  1.0 607644 43156 ?  Ssl  02:03  0:01 PM2 v4.2.3: God Daemon (/root/.pm2)
```



## Run App on Startup

```bash
# enable pm2 starup hook (e.g. create service with name `pm2-root.service`)
pm2 startup

# save pm2 status to `~/.pm2/dump.pm2`
pm2 save

# restore pm2 status
pm2 resurrect
```

https://pm2.keymetrics.io/docs/usage/startup/



## Create or Restart App

Create App if not exists, and start / restart the app.

```bash
#!/usr/bin/env bash

function restartPm2Script() {
  NAME=$1;
  SCRIPT=${2:-start}
  pm2 describe $NAME > /dev/null
  RUNNING=$?
  if [ "${RUNNING}" -ne 0 ]; then
    echo "start app '$NAME'..."
    pm2 start npm --name $NAME -- run $SCRIPT
  else
    echo "restart app '$NAME'..."
    pm2 restart $NAME
  fi;
}

restartPm2Script myapp start
```

