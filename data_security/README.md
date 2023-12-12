# Data Security

# Red Team

# Blue Team
To ensure that we will constant backups being stored in a dedicated server, we will create a script that zips and archives a folder `/home/kali/importantFiles` everyday at midnight, and uploads it to another machine through scp. `nano backup.sh`:

```
#!/bin/bash

sourceDir="/home/kali/importantFiles"
timestamp=$(date +"%Y%m%d_%H%M%S")
zipFilename="importantFiles_$timestamp.tar.gz"
tar -zcvf "$zipFilename" "$sourceDir"

scp "$zipFilename" user@192.168.0.10:/home/backups/kaliBackups/

rm "$zipFilename"
```

Grant it execute permissions with `chmod +x backup_and_transfer.sh` and then let's add it to our crontab `crontab -e`:

```
0 0 * * * /home/kali/backup.sh
```

This way, if our kali machine is compromised, our important files were upload to a different host the night before - working without the data for the previous day is definitely better than losing everything.