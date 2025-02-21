## **Note**
Once I had a Metabase server and I wanted to back it up, so I asked ChatGPT and this guide was born. It can be used for any other Docker image.

## **Disclaimer**
This guide is provided as-is without any guarantees. You are responsible for ensuring the accuracy and completeness of your backups and for any consequences that may arise from following this guide.

# **Backup Entire Server - Docker Installation**

If you have a Docker installation of Metabase, you can back up the entire server by focusing on the Docker container and its volumes. Here’s how you can back up the entire server effectively:

---

## **Step 1: Identify the Docker Setup**
1. Check the name of your Metabase container:
   ```bash
   docker ps
   ```
   Look for the container running Metabase (e.g., `metabase/metabase`).

2. Identify volumes attached to the container. These hold the configuration and database files:
   ```bash
   docker inspect <container_name> | grep Mounts
   ```

   Example output:
   ```json
   "Source": "/var/lib/docker/volumes/metabase_data/_data",
   ```

---

## **Step 2: Backup Docker Volumes**
To ensure all persistent data is safe, you need to back up the volumes associated with Metabase.

### **Method 1: Copy Volume Data**
1. Stop the Metabase container to ensure no changes occur during backup:
   ```bash
   docker stop <container_name>
   ```

2. Copy the volume data to your backup directory:
   ```bash
   docker cp <container_name>:/path/in/container /path/to/backup/
   ```
   Or directly from the host volume:
   ```bash
   cp -r /var/lib/docker/volumes/metabase_data/_data /path/to/backup/
   ```

3. Restart the container after the backup:
   ```bash
   docker start <container_name>
   ```

---

## **Step 3: Backup the Entire Container**
You can create a backup of the entire Docker container as a tar file.

1. Stop the Metabase container:
   ```bash
   docker stop <container_name>
   ```

2. Commit the container state to an image:
   ```bash
   docker commit <container_name> metabase_backup:latest
   ```

3. Save the image as a tar file:
   ```bash
   docker save -o /path/to/backup/metabase_backup.tar metabase_backup:latest
   ```

4. Restart the container:
   ```bash
   docker start <container_name>
   ```

To restore:
   ```bash
   docker load -i /path/to/backup/metabase_backup.tar
   docker run --name metabase_restored -d metabase_backup:latest
   ```

---

## **Step 4: Automate the Backup**
You can schedule volume or container backups using a **cron job**. For example:
1. Create a shell script `backup_metabase.sh`:
   ```bash
   #!/bin/bash
   BACKUP_DIR="/path/to/backup/$(date +%F)"
   mkdir -p $BACKUP_DIR

   # Stop container
   docker stop metabase

   # Backup volume
   cp -r /var/lib/docker/volumes/metabase_data/_data $BACKUP_DIR

   # Backup container
   docker commit metabase metabase_backup:latest
   docker save -o $BACKUP_DIR/metabase_backup.tar metabase_backup:latest

   # Start container
   docker start metabase
   ```

2. Schedule it with `cron`:
   ```bash
   crontab -e
   ```
   Add the job:
   ```bash
   0 2 * * * /path/to/backup_metabase.sh
   ```

---

## **Step 5: Test Restoration**
Always test restoring from your backups to ensure the process works as expected:
1. Restore the volume data to the original location.
2. Load the tar image and start a new container.
3. Verify that the restored instance works properly.

---
