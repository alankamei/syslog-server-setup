# Install and Configure Syslog-ng Server

1. **Update and Install Syslog-ng**:
   ```bash
   sudo apt-get update
   sudo apt-get install syslog-ng


2. **Configure Syslog-ng**
    ```bash
sudo nano /etc/syslog-ng/syslog-ng.conf


3.  **Add the following lines to configure the server to listen on port 514**

source s_syslog {
    system();
};

destination d_syslog {
    file("/var/log/sophos.log" template("${YEAR}-${MONTH}-${DAY}T${HOUR}:${MINUTE}:${SECOND}.log" owner("syslog" group("syslog") perm(0640)));
};

filter f_sophos {
    program("sophos");
};

log {
    source(s_syslog);
    filter(f_sophos);
    destination(d_syslog);
};


4. **Restart syslog-ng**
    ```bash
sudo systemctl restart syslog-ng

5. **Configure your firewall to allow UDP 514**
## Configure Sophos firewall to send logs to ubuntu Server 
#   Log in to Sophos Firewall:
#   
#   Go to System Services > Log Settings.
#   
#   Add Syslog Server:
#   
#   Click Add to add a new syslog server.
#   
#   Enter a name for the server (e.g., "UbuntuServer").
#   
#   Enter the IP address of your Ubuntu server.
#   
#   Set the port to 514.
#   
#   Optionally, configure secure log transmission if needed.
#   
#   Select Logs to Send:
#   
#   Select the logs you want to send to the syslog server (e.g., firewall logs, IPS logs, etc.).
#   
#   Save and Apply:
#   
#   Click Save to apply the changes.



6.  Verify logs on ubuntu server 
    ```bash
sudo tail -f /var/log/sophos.log