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
To set-up your database, you first need to intiate your database session and create a database. Therefore, open a terminal from inside your Docker image. Please note that you will need to exchange the name to the name of your InfluxDB container, mine is called `blissful_wilson`:
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
Please note that you need to `use`the database before, as show above. Now you can also show the last entries of your measurement:
```
select * from temperature
```

## Load & Run your program in Node-RED
To open Node-RED, type again your Rasperry's IP-address and the correct port for Node-RED into your browser:
```
http://192.168.21.96:1880
```
Afterwards, the easiest way is to import the complete function code, called a "Flow". Therefore, click on the menu symbol in the upper left corner and select "Import":

![Node-RED Import Function](/Images/NodeRed.png)

In the pop-up window, copy in the JSON-file [here](/Code/Node-RED Flows/) in the GitHub-repository.
![Copy in the code from the clipboard](/Images/NodeRedImport.PNG)

Do the same for both the Client- and Server-side Flows. Before you can deploy the code, you need to do some additional parameterization. For the sensor, open the sensor Block (called "rpi-dht22") and select the correct GPIO pin that your are actually using. 
![DHT Sensor Node](/Images/dhtsensor.PNG)

Then, you need to adapt the MQTT broker settings. This needs to be done for each measurement and both on client and server side. Click again on the corresponding nodes:

![MQTT Settings](/Images/Mqtt1.PNG) 

Besides the topics you want to use, you also need to change the address and port of the server, if you don't run both sides on the same device or if you bounded it to a non-default port:

![MQTT Server Settings](/Images/MqttServerSettings.PNG)

Afterwards, you also need to adapt the settings of the InfluxDB Nodes in the Server Flow. It is similar to the MQTT-part, so select the measurement data point you want to write onto in the database:

![InfluxDB Settings](/Images/InfluxDbSettings.PNG)

Also adapt your server settings as well:

![InfluxDB Server Settings](/Images/InfluxDbServer.PNG)

## Build your Grafana Dashboard
### Connect your database first
As the other tools, Grafana can be accessed from the Web-Browser, as well:
```
http://192.168.21.96:3000
```
Before you can create a dashboard, first we need to connect the InfluxDB as a data source. Therefore, move to "Settings" and "Data Source":

![Data Source](/Images/GrafanaDataSourvce.PNG)

Afterwards, add a new data source and select "InfluxDB" as a type:

![Add data source](/Images/DataSourceInflux.PNG)

Provide the correct settings to establish a connection:

![Database server settings](/Images/IoTServerSettings.PNG)

Don't forget to scroll down and select the actual database (in this case `air`) you want to access:

![Select the database you want to use](/Images/IotServerSettings2.PNG)

### Create your Dashboard
Now you are finally ready to create your dashboard. Click onto "Create" to start:

![Create Dashboard](/Images/CreateDashboard.PNG)

Then, add a new panel:

![Add a new Panel](/Images/AddNewPanel.PNG)

For the new panel, you will be asked to enter the correct information for your data point. Therefore, select the Data Source you want to connect (our InfluxDB `air` Database) and the measurement point you want to read (so it will be either `temperature`or `humidity`):

![Make your Measurement Settings](/Images/HumiditySettings.PNG)

You can give your panel a name, e.g. "Humidity", on the right side, then click the "Apply" Button and your are ready to got. Add another panel (from a button in the upper right of your dashboard) and make also some settings about the time horizon you want to monitor, as well as the automatic update rate of your Panels:

![Dashboard Settings](/Images/AddPanel.PNG)

Afterwards, you have successfully set-up your IoT Gateway and server, Congratulations!

![Final Dashboard View](/Images/Dashboard.PNG)
