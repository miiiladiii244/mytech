# How to create a Systemd service in Linux

**Date:** 19th August, 2023  
**Source:** [Shubham Dipt Blog](https://www.shubhamdipt.com/blog/how-to-create-a-systemd-service-in-linux/)

At times you create a script and then you want to have the scripts controlled by systemd or in some cases you wish to have the scripts getting restarted by itself when it is killed due to some reason. In such cases, systemd in Linux helps to configure services which can be managed. To do so, follow the following steps:

1. Navigate to the systemd directory:

   ```bash
   cd /etc/systemd/system
   ```

2. Create a file named `your-service.service` and include the following:

   ```ini
   [Unit]
   Description=<description about this service>

   [Service]
   User=<user e.g. root>
   WorkingDirectory=<directory_of_script e.g. /root>
   ExecStart=<script which needs to be executed>
   # optional items below
   Restart=always
   RestartSec=3

   [Install]
   WantedBy=multi-user.target
   ```

## For Python specific projects which include virtual environment:

1. Create a service file with the following content:

   ```ini
   [Unit]
   Description=<project description>

   [Service]
   User=<user e.g. root>
   WorkingDirectory=<path to your project directory containing your python script>
   ExecStart=/home/user/.virtualenv/bin/python main.py
   # optional items below
   Restart=always
   RestartSec=3
   # replace /home/user/.virtualenv/bin/python with your virtualenv and main.py with your script

   [Install]
   WantedBy=multi-user.target
   ```

OR

2. Create a service file with the following content:

   ```ini
   [Unit]
   Description=<project description>

   [Service]
   User=<user e.g. root>
   WorkingDirectory=<path to your project directory>
   ExecStart=/bin/bash -c 'cd /home/ubuntu/project/ && source venv/bin/activate && python test.py'
   ```

3. Enable and start the service:
   ```bash
   sudo systemctl enable your-service
   sudo systemctl start your-service
   ```
