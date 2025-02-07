## All persons including trans deserve healthcare, education and equal rights. Nobody is free until we are all free!

## What is it?
A bash script to gracefully stop/restart and perform local backups of a Minecraft server running in Screen on Ubuntu. Back up full server files, just worlds or just plugins with in-game warnings to tell players the server is restarting. Easily automated with crontab.

## Setup   
1. Open the script in a text editor and change these variables at the top:  
- **serverDir** = Your root server directory. (dont include closing "/")  
- **backupDir** = The location to backup the compressed files to. The folder must exist already. (dont include closing "/")   
- **startScript** = The command to restart the server.  
- **gracePeriod** = Time to wait between warning in-game players and stopping server. M for minutes, s for seconds.  
- **serverWorlds** = A list of the world folder names. Includes defaults, add any of your custom worlds here. 

2. Manually start a screen session with ``screen -S <screen-id>`` and start your Minecraft server within the screen session. Ensure there is only 1 running screen session with ``screen -ls``. (or see below for how to automate)  

3. Deattach from the screen session with ``ctrl + a + d`` and run the mc-backup.sh script from a SSH/terminal session outside of screen when you're ready to initiate a backup. Re-attach to the screen session with ``screen -r <screen-id>`` if needed. (or see below for how to automate)    

(Optional) Auto-start minecraft server and screen at system boot:  
- `crontab -e`
- add `@reboot sleep 60 && bash /path/to/server/start.sh` to end of crontab file
- in your Minecraft server start.sh:    
```!#/bin/sh  
cd /path/to/server  
screen -dmS <screen-id>
screen -p 0 -X stuff 'java -Xms<RAM>G -Xmx<RAM>G -jar paper*\n'  
```

(Optional) Automate mc-backup.sh with [Crontab](https://ostechnix.com/a-beginners-guide-to-cron-jobs/) (examples below):  
- `crontab -e`
- Restart server every day at noon: ```00 12 * * * bash /home/J-Bentley/mc-backup.sh -r```
- Backup world files every day at midnight: ```00 24 * * * bash /home/J-Bentley/mc-backup.sh -w```
- Full server backup every monday at 1 AM: ```00 1 * * 1 bash /home/J-Bentley/mc-backup.sh```

## Usage  
``bash mc-backup.sh [-h , -r , -w , -p, -pc] ``

- **No args:** Gracefully stops the server if its running, compresses entire server directory to backup location and restarts server.  

- **-h | --help:** Shows modes available.   

- **-r | --restart:** Saves & restarts server with no backup made.  

- **-w | --worlds:** Gracefully stops the server, compresses world directories and restarts server.   

- **-p | --plugins:** Gracefully stops the server, compresses entire plugin directory and restarts server. 

- **-pc | --pluginconfig:** Gracefully stops the server, compresses plugin config directories and restarts server. Ignores plugin .jars.  

## Caveats
- Only 1 or no arg can be called at a time.
- Only 1 screen session can be running on the system.
- If the server is offline when mc-backup is run, won't restart it after a backup unless in restartonly mode (-r). (submit an issue if you think this should be changed)  
