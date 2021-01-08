# nord_ip_loop
Shell Script to switch between all possible Nord VPN servers at selected time interval.

# Make sure to install nordvpn in your Linux system:

1.Download the NordVPN Linux client by opening the terminal, writing the command below, and following any on-screen instructions:

curl -sSf https://downloads.nordcdn.com/apps/linux/install.sh

Note: If you do not have a curl package, evidenced by the fact that the above does not work, you can alternatively use this command:
wget -qO - https://downloads.nordcdn.com/apps/linux/install.sh

2. Log in to your NordVPN account:

nordvpn login


# Installation and Usage of Script

1.Download/Copy the script to your bin directory and provide execution permissions:

cp nord_loop /usr/bin/ 
chmod +x nord_loop

2. Run the Script and select time interval to switch servers:

nord_loop

Enter time to switch in seconds: #select whatever time you want

Press [CTRL+C] to stop the script and switch to original IP

