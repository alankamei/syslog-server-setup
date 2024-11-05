1.   Verify network communication 
    ```bash
    ping <Ubuntu-server_IP>
    ping <Sophos-firewall-IP>
2.   Installing rsyslog
    ```bash
    sudo apt update
    sudo apt install rsyslog

3.   Now, we need to tell rsyslog to accept incoming log messages over UDP and/or TCP on port 514. This is done by editing the rsyslog configuration.
    ```bash
    sudo nano /etc/rsyslog.conf

  ** Find and uncomment (remove the (h) at the start of) the following lines to enable UDP and/or TCP syslog reception on port 514.**
    **This will allow your server to listen for syslog    messages from other devices (like your Sophos Firewall):**

    ----rsyslog.conf----

            module(load="imudp")  # Enable UDP support
            input(type="imudp" port="514")  # Listen on UDP port 514

            module(load="imtcp")  # Enable TCP support
            input(type="imtcp" port="514")  # Listen on TCP port 514


4.   Now that you've enabled remote syslog logging, restart the rsyslog service to apply these changes:
    ```bash
    sudo systemctl restart rsyslog


5.  Create a new configuration file in /etc/rsyslog.d/ to filter and store the Sophos logs in a separate file (e.g., /var/log/sophos.log).
    ```bash
    sudo nano /etc/rsyslog.d/30-sophos.conf


6.   In this file, we’ll add a rule to capture logs from the specific IP address of your Sophos Firewall. Replace <Sophos-firewall-IP> with the 
    actual IP address of your Sophos Firewall.
    ```bash
    if $fromhost-ip == '<Sophos-firewall-IP>' then /var/log/sophos.log
    & stop


    **if $fromhost-ip == '<Sophos-firewall-IP>' tells rsyslog to filter logs coming specifically from the Sophos firewall’s IP address.**
    **then /var/log/sophos.log ensures those logs are stored in the /var/log/sophos.log file.**
    **& stop prevents the logs from being written to other log files (like /var/log/syslog).**


7.   After creating the custom configuration file, restart rsyslog to load the new settings:
    ```bash
    sudo systemctl restart rsyslog



8.   Make sure the log file /var/log/sophos.log has the correct permissions so rsyslog can write to it.
    ```bash
    sudo touch /var/log/sophos.log  # Create the log file if it doesn't exist
    sudo chown syslog:adm /var/log/sophos.log
    sudo chmod 640 /var/log/sophos.log






9.   Configure the Sophos Firewall to Send Logs to the Ubuntu Server
       Now, let's make sure the Sophos Firewall is configured to send logs to the Ubuntu server.
    
       Log into the Sophos Firewall's Web Admin Interface.
    
       Navigate to System → Logging and Reporting.
    
       Under Log Settings, ensure that Syslog is enabled.
    
       Add a new Syslog Server:
    
       IP Address: Enter the IP address of your Ubuntu server.
       Port: Set it to 514 (the default Syslog port).
       Log Level: Set it to the level of logs you wish to capture (e.g., Informational or Debug).
       Apply the changes and save the configuration.






10. Test the Configuration
    After setting everything up, it’s time to test the configuration.
    Generate Some Logs on the Sophos Firewall:
    This can be done by triggering some events (like logging in or generating firewall activity) that will create log messages.
    Check the Log File on Ubuntu: On your Ubuntu server, check the log file /var/log/sophos.log to ensure it’s receiving the logs from the Sophos firewall.
    ```bash
    tail -f /var/log/sophos.log


    You should start seeing logs from the Sophos firewall appearing in this file.



11. Verify Everything is Working
    Test Logs:

    You can use a test message to make sure the log reception is working. From the Ubuntu server, run:

   ```bash
    logger -p local0.info "Test log message from Ubuntu server"




12. Monitor Logs Regularly
    Once everything is configured, your Ubuntu server will continue to receive logs from the Sophos firewall. You can use tools like logrotate to manage log sizes and rotation if you  expect a high volume of logs.

    You can check the logs periodically by running:
    ```bash    
    tail -f /var/log/sophos.log
