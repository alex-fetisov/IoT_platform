# IoT_platform (draft)

Inspired by https://larsbergqvist.wordpress.com/2017/03/02/influxdb-and-grafana-for-sensor-time-series/

This project it to learn and try to build IoT platform, which can fulfill various use cases; for example, sensor data logging and monitoring.
The solution architecture is quite generic for modern applications and can be used for data collection, manipulation, analysis and visualisation in many other domains, such as Telco (e.g. performance management), Finance, etc.

![IoT_solutuion architechture](https://user-images.githubusercontent.com/19657873/34466253-636eeba2-ef21-11e7-9405-19108b6e1a0f.PNG)

##Components
We are going to use the follwoing components:
Message broker - MQTT mosquitto
Time series database - InfluxDB
Front-end visualisation - Grafana

##Implementation steps

Will be using Win10 to access Raspberry PI3 via SSH to perform initial system setup.

	1. Run Cygwin terminal (used Windows 10)
	
  2. Login into PI box via SSH
	
  3. Update the list of available packages and their versions with the command
		a. apt-get update
		b. sudo access maybe required, i.e. sudo apt-get update
	
  4. Upgrade your installed packages to a latest version:
		a. apt-get upgrade
		b. sudo access maybe required, i.e. sudo apt-get upgrade
	
  5. Install MQTT message broker
		a. apt-get install mosquitto
	
  6. Install MQTT clients
		a. apt-get install mosquitto-clients
	
  7. Create and subscribe to the "test_topic"
		a. mosquitto_sub -d -t test_topic
		b. The output will look like that:
			afetisov@pi:~ $ mosquitto_sub -d -t test_topic
			Client mosqsub/1451-pi sending CONNECT
			Client mosqsub/1451-pi received CONNACK
			Client mosqsub/1451-pi sending SUBSCRIBE (Mid: 1, Topic: test_topic, QoS: 0)
			Client mosqsub/1451-pi received SUBACK
			Subscribed (mid: 1): 0
			Client mosqsub/1451-pi sending PINGREQ
			Client mosqsub/1451-pi received PINGRESP
			
	8. Open the second Cygwin terminal window, login to PI so that you have two simultaneous PI SSH sessions running and run the following command to send a message to raspberry: 
		a. mosquitto_pub -d -m 'This is the first message in the test_topic' -t test_topic
		b. Terminal window:
			afetisov@pi:~ $ mosquitto_pub -d -m 'This is the first message in the test_topic' -t test_topic
			Client mosqpub/1467-pi sending CONNECT
			Client mosqpub/1467-pi received CONNACK
			Client mosqpub/1467-pi sending PUBLISH (d0, q0, r0, m1, 'test_topic', ... (43 bytes))
			Client mosqpub/1467-pi sending DISCONNECT
		c.  the first SSH session will show:
			Client mosqsub/1451-pi received PUBLISH (d0, q0, r0, m0, 'test_topic', ... (43 bytes))
			This is the first message in the test_topic
	
  9. Install InfluxDB
		a. Find out which OS version we are working on
			i. lsb_release -a
		b. enable https:\\ It is not enabled in the default apt install for Rasberry Pi
			i. sudo apt install apt-transport-https
		c. Install the latest Influx DB using the link below. At the moment of writing this guide 1.4 was the latest version
			i. https://docs.influxdata.com/influxdb/v1.4/introduction/installation/
		d. Try to run influxdb using the command 
			i. Influxdb
			ii. Output:
				afetisov@pi:~ $ influx
				Connected to http://localhost:8086 version 1.4.2
				InfluxDB shell version: 1.4.2
	
  10. Modify /etc/influxdb/influxdb.conf:
	[http]
	  enabled = true                   #HTTP endpoint is enabled.
	  bind-address = ":8086"    #bind address for HTTP service
	  auth-enabled = false         #require user auth over HTTP/HTTPS
	
	11. Restart influxdb service
		a. sudo service influxd restart
	
  12. Install Grafana (https://www.circuits.dk/install-grafana-influxdb-raspberry/)
		a. sudo apt-get install apt-transport-https curl
		b. curl https://bintray.com/user/downloadSubjectPublicKey?username=bintray | sudo apt-key add -
		c. Update your apt source
			echo "deb https://dl.bintray.com/fg2it/deb jessie main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
		d. Install Grafana:
			sudo apt-get update
			sudo apt-get install grafana
		e. Start the server
			i. sudo service grafana-server start 
      That will start grafana-server process as grafana user.  The default HTTP port is 3000 and default user and group is admin/admin
			ii. sudo update-rc.d grafana-server defaults <- To configure the Grafana server to start at boot time
      
      
      To be continued...
