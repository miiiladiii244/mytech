# How to create a Systemd service in Linux

**Source:** [Shubham Dipt Blog](https://www.shubhamdipt.com/blog/how-to-create-a-systemd-service-in-linux/)

Steps:

1. Navigate to the systemd directory:

   ```bash
   cd /etc/systemd/system
   ```

2. Create a file named `your-service.service`:

   ```ini
   [Unit]
   Description=<description about this service>

   [Service]
   User=<user e.g. root, consider security principles and best practices>
   WorkingDirectory=<directory_of_script e.g. /root>
   ExecStart=<script which needs to be executed>
   Restart=always
   RestartSec=3

   [Install]
   WantedBy=multi-user.target
   ```

3. Reload the service files to include the new service:

   ```bash
   sudo systemctl daemon-reload
   ```

4. Enable and start the service:

   ```bash
   sudo systemctl enable your-service
   sudo systemctl start your-service
   ```

5. Manging the service:
   ```bash
   sudo systemctl status your-service
   sudo systemctl stop your-service
   sudo systemctl disable your-service
   ```
