## Bamboo Installation:


# Requirement:
	* Install Java OpenJDK-8-JDK
	* Bamboo install Package

# Install Java:
	apt-get install software-properties-common
	add-apt-repository ppa:openjdk-r/ppa
	apt-get update
	apt-get install openjdk-8-jdk -y
	java -version

# Install Bamboo:
	cd /opt
	wget https://www.atlassian.com/software/bamboo/downloads/binary/atlassian-bamboo-6.8.0.tar.gz
	tar -xvf atlassian-bamboo-6.8.0.tar.gz
	mv atlassian-bamboo-6.8.0 bamboo
	cd /opt/bamboo/
	
  	vi /opt/bamboo/atlassian-bamboo/WEB-INF/classes/bamboo-init.properties
	---------------------------------------------------------------------
	bamboo.home=/home/bamboo/bamboo-home
	-------------------------------------------------------
	
  	mkdir -p /home/bamboo/bamboo-home
	useradd -s /bin/bash bamboo
	
	vi /etc/systemd/system/bamboo.service
	------------------------------------------------------------
	[Unit]
	Description = Atlassian Bamboo
	After = syslog.target network.target

	[Service]
	
	Type = forking
	User = bamboo
	ExecStart = /opt/bamboo/bin/start-bamboo.sh
	ExecStop = /opt/bamboo/bin/stop-bamboo.sh
	SuccessExitStatus = 143

	[Install]
	WantedBy = multi-user.target
	------------------------------------------------------------
	
	systemctl enable bamboo.service
	
	if [ -f /etc/systemd/system/*.wants/bamboo.service ]; then echo "On"; else echo "Off"; fi
	
	vi /etc/init.d/bamboo
	-------------------------------------------------------------------
	#!/bin/sh
	set -e
	
	#### VARIABLES ####
	
	# Name of app ( bamboo, Confluence, etc )
	APP=bamboo
	# Name of the user to run as
	USER=bamboo
	# Location of application's bin directory
	BASE=/opt/bamboo
	
	case "$1" in
	# Start command
	start)
		echo "Starting $APP"
		/bin/su - $USER -c "export BAMBOO_HOME=${BAMBOO_HOME}; $BASE/bin/startup.sh &> /dev/null"
		;;
	# Stop command
	stop)
		echo "Stopping $APP"
		/bin/su - $USER -c "$BASE/bin/shutdown.sh &> /dev/null"
		echo "$APP stopped successfully"
		;;
	# Restart command
	restart)
			$0 stop
			sleep 5
			$0 start
			;;
	*)
		echo "Usage: /etc/init.d/$APP {start|restart|stop}"
		exit 1
		;;
	esac
	exit 0
	----------------------------------------------------------------------
	
	chmod 755 /etc/init.d/bamboo
	chown -R bamboo:bamboo /home/bamboo/bamboo-home
	chmod 777 /home/bamboo/ -R
	
	service bamboo start

# Go to UI and give below command
	<IP-address>:8085
# Create Atlassian account and goto licenses and create new license
	https://id.atlassian.com/
	
![image](https://user-images.githubusercontent.com/68885738/89013993-7d2abd80-d332-11ea-9aaa-59aa8b2462f5.png)

Next

![image](https://user-images.githubusercontent.com/68885738/89014104-a51a2100-d332-11ea-97ce-d17efb139a64.png)

Next

![image](https://user-images.githubusercontent.com/68885738/89014188-c5e27680-d332-11ea-831c-38d382af309b.png)

Verify this with your email

Give below link

https://my.atlassian.com/products/index

![image](https://user-images.githubusercontent.com/68885738/89015157-3dfd6c00-d334-11ea-8ec8-1901084a45f7.png)

Click on New Trial License

![image](https://user-images.githubusercontent.com/68885738/89015269-62f1df00-d334-11ea-925d-b1ff02b0b6e6.png)

![image](https://user-images.githubusercontent.com/68885738/89015383-93397d80-d334-11ea-8abf-71606f84312e.png)

Give Server ID which one need license and click on Generate License

Copy License and paste in our Bamboo installation and then complete steps


