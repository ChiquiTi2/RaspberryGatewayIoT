# How To Read DHT Sensor Data & Display it the IoT-way
For our project, we will start from the hardware and will go through all the steps towards visualization.

## Hardware connection
The hardware installation can be carried out like shown in the schematics, which I found [here](https://buyzero.de/blogs/news/tutorial-dht22-dht11-und-am2302-temperatursensor-feuchtigkeitsensor-am-raspberry-pi-anschliessen-und-ansteuern):
![Alt Schematic sensor Connection](https://cdn.shopify.com/s/files/1/1560/1473/files/DHT22_wiring_up_to_pi_1024x1024.png?v=1553198063)

## Manage & Start your Containers via Portainer
The easiest way to start, stop & configure your containers is via the Web-Interface of Portainer. Therefore, type the path of your portainer instance, consisting of your Rasperry's IP address and the port, into the browser:
```
http://192.168.21.96:9000
```

Afterwards, you can go to your "Container" tab and start the containers vial selecting them and pressing "Start".
![Start Containers from Portainer](/Images/Portainer.PNG)

## Set-up your InfluxDB database
To set-up your database, you first need to intiate your database session and create a database. Therefore, open a terminal from inside your Docker image. Please note that you will need to exchange the name to the name of your InfluxDB container, mine is called `<blissful_wilson>`:
```
docker exec -it blissful_wilson/bin/bash
influx -precision rfc3339
create database air
```
Now we will have a database called air, and we can check whether it is there:
```
show databases
use air
```
If it is working fine, you can now move on with your Node-RED program. After that, you can check again from the terminal whether datapoints have been added correctly:
```
show measurements
```
Please note that you need to `<use>`the database before, as show above. Now you can also show the last entries of your measurement:
```
select * from temperature
```

### Load & Run your program in Node-RED
