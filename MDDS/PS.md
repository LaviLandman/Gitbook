# PS Instructions

Backup the old professional service src directory if exist

`cp -R /home/ibex/src/professional /home/ibex/src/professional_backup`

Update or create the professional service env file/home/ibex/src/professional/env.sh file and add Create the professional folder if missing

`mkdir \-p /home/ibex/src/professional`

Edit the configuration file vim /home/ibex/src/professional/env.sh with the content below

```
#!/bin/bash  
export PROFESSIONAL_VERSION="3.6.0.3"
```

Update or install the professional service

Run the following commands

```
# Load the version
source ****/home/ibex/src/professional/env.sh

# Pull professional service docker image and tag it -
aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 705615858573.dkr.ecr.us-east-2.amazonaws.com
docker pull 705615858573.dkr.ecr.us-east-2.amazonaws.com/ibex/professional:$PROFESSIONAL_VERSION
docker tag 705615858573.dkr.ecr.us-east-2.amazonaws.com/ibex/professional:$PROFESSIONAL_VERSION ibex-professional:$PROFESSIONAL_VERSION

# Extract professional installation files
docker run --rm -v /home/ibex/src/professional:/out --entrypoint cp ibex-professional:$PROFESSIONAL_VERSION -R /installation/. /out/

# Change ownership of professional directory
sudo chown ibex:ubuntu -R /home/ibex/src/professional
```



Update the configuration file /home/ibex/src/professional\_backup/config.yaml

Option 1: Restore the configuration file from the backup

`cp -R /home/ibex/src/professional_backup/config.yaml /home/ibex/src/professional`

Option 2: Update the configuration file manually

`sudo vim /home/ibex/src/professional/config.yaml`

Install professional service as a systemd service

`cd /home/ibex/src/professional/ && sudo ./setup.sh`

Verification

`sudo systemctl status ibex-professional.service`

Verify the professional service is running

Remove old professional images

Get a list of old images

`docker images | grep professional | grep -v 3.6.0.3 | awk '{print $1" "$2}' | awk '{sub(/ /, ":")}1'`

Remove old images

`docker rmi INPUT_FROM_PREVIOUS_STEP`

💡 Note: Replace INPUT\_FROM\_PREVIOUS\_STEP with the output of the relevant command
