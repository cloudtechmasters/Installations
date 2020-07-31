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
