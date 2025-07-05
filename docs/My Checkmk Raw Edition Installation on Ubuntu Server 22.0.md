This guide documents how I installed Checkmk Raw Edition version 2.4.0p6.cre on my Ubuntu Server 22.04 LTS. It includes key setup details, troubleshooting tips, and how I resolved Apache configuration errors that caused initial 404 Not Found issues.

🔧 Setup Overview
Component	Value
OS	Ubuntu Server 22.04 LTS
Checkmk Version	2.4.0p6 (Raw Edition)
Server IP	10.0.1.207
User	sudo-enabled user (e.g., billtks)
Internet Access	Required

🛠️ Step-by-Step Installation
1. Download the Checkmk Package
bash
Αντιγραφή
Επεξεργασία
cd ~
wget https://download.checkmk.com/checkmk/2.4.0p6/check-mk-raw-2.4.0p6_0.noble_amd64.deb
☑️ Note: Ensure you're using the correct .deb package for your Ubuntu version (noble corresponds to 24.04, but it may still work if dependencies are met).

2. Install the Package
bash
Αντιγραφή
Επεξεργασία
sudo dpkg -i check-mk-raw-2.4.0p6_0.noble_amd64.deb
🧠 You'll be prompted for your sudo password.

3. Create a Monitoring Site
bash

sudo omd create homelab
📌 Important: The admin password for cmkadmin is shown immediately after running this command. Copy it!

Example output:

pgsql

The admin user for the web applications is cmkadmin with password: bS41X2IB2E7B
What I Learned About the Password:
Initially, I missed this password and tried to find it under:

bash
sudo omd su homelab
cat ~/tmp/password.secret
But this file didn’t exist. The only reliable way to get the password was directly from the omd create command output.

4. Fixing Apache: My Biggest Hurdle
Apache failed to start with:

404 Not Found in the browser

Or errors like:

nginx

Syntax error on line 1 of /etc/apache2/conf-enabled/zzz_omd.conf
The original line in zzz_omd.conf:

apache

Include /omd/apache/*.conf
This caused:

sql

No matches for the wildcard '*.conf' in '/omd/apache/', failing
✅ My Fix
Edit the config file:

bash

sudo nano /etc/apache2/conf-enabled/zzz_omd.conf
Change this line:

apache

Include /omd/apache/*.conf
To this:

apache

IncludeOptional /opt/omd/sites/homelab/tmp/apache/*.conf
Save and exit (Ctrl+O, Enter, Ctrl+X).

Test the Apache config:
bash

sudo apache2ctl configtest
✅ Output should include: Syntax OK

⚠️ You can ignore the Could not reliably determine the server's fully qualified domain name warning.

5. Start Apache & Your Checkmk Site
Restart Apache:

bash

sudo systemctl restart apache2.service
Check its status:

bash

systemctl status apache2.service
Start your Checkmk site:

bash

omd start homelab
6. Access the Web Interface
Open your browser and go to:

arduino

http://10.0.1.207/homelab/
Login credentials:

Username: cmkadmin

Password: Use the one from the omd create output.

🛡️ Change the Password
On first login, Checkmk will prompt you to set a new, secure password.

📸 (Optional) Screenshot
📷 Refer to image_f6068a.png (if committed to your repo) to see the config file edit in Nano.

✅ Summary
This install required some manual Apache config corrections, but in the end, Checkmk Raw Edition 2.4.0p6 ran smoothly on Ubuntu 22.04. I documented this as a reference for anyone troubleshooting similar issues.
