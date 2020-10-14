# Prepare your Raspberry Pi

## Needed equipment
For our project, your will need the following equipment:
* A Raspberry Pi, e.g. the model 3B+, 4 or 4B
* A micro SD card, min. 8GB (but for more storage, choose something with more space) and an adapter so you can use it with your computer 
* A micro USB Cable for power supply, e.g. from your cellphone
* A standard ethernet cable

For the sensor connection, I'd suggest you get the following equipment:
* A breadboard
* Some cables and jumpers on different sizes
* A breakout adapter and ribbon cable to connect to the GPIOs
* most important: sensors, e.g. a temperature & humidity sensor like the DHT11 or DHT22 I used for the example

## Set up your Raspberry Pi OS
To setup your Raspberry Pi, first [download the operating-system (OS) image](https://www.raspberrypi.org/downloads/) from the Raspberry Website.

Download the "Raspberry Pi Imager" and select an operating system. For our example, the "Raspberry Pi OS Lite" is sufficient, as we don't need a desktop environment. 
Insert the SD card into your computer and flash the image onto it. 

Afterwards, you need to take out and put the card back into your computer, for some addon. Navigate to the SD card, then create an empty file called "SSH", without any file type extension.

Now your are ready to insert the SD card into the Raspberry Pi and power it up. 

## Login to your Pi via SSH
To connect to your Raspberry Pi, a program like [PuTTY Terminal](https://www.putty.org/) can be used. However, before you can establish a connection, you will need to know the IP address of your Pi, so look it up in your router. I'd suggest you modify the settings so your router will always give the same address to your Raspberry Pi. Afterwards, open PuTTY and enter the IP of your Pi. For the port, select 22 as the standard SSH port. You can now also save the connection settings for later sessions, so you don't need to remember the IP address at all. 

Afterwards, a terminal window will open and you will be asked to enter the user credentials. The default settings are:
* user: pi
* password: raspberry

Of course it's suggested to change it soon.

The first steps on your pi will be checking for updates:

```
sudo apt-get update
sudo apt-get upgrade -y
```

## Install the needed tools
For our project, we will only use open-source and easily usable tools:
* Node-RED
* Python 3
* Docker
* Portainer
* Eclipse Mosquitto
* Influx DB
* Grafana

All except for Node-Red and Python will be run in Docker containers. The reason is the ease during the installation and update and less worries with messing up your system. As Node-Red and Python will need to access your GPIO pins to connect to your sensors, they need a deeper access to your Pi, so I found it works best to install them directly instead of a docker image.

### Node-RED
To install Node-Red in a single step, use the following command:
```
bash <(curl -sL https://raw.githubusercontent.com/node-red/linux-installers/master/deb/update-nodejs-and-nodered)
```

After installation, you want to avoid that Node-RED will later overflow your memory:
```
node-red-pi --max-old-space-size=256
```

Then enable your Node-RED to run as service:
```
sudo systemctl enable nodered.service
```
For our later application, we will already install some needed libaries to access the database and the sensor connection:
```
sudo npm install --unsafe-perm -g node-dht-sensor
sudo npm install --unsafe-perm -g node-red-contrib-dht-sensor
sudo npm install node-red-contrib-influxdb
```

### Docker
Docker can also be installed with a single command:
```
sudo curl -fsSL https://get.docker.com |sh
```
To check whether docker has been installed, you can run the following commands:
```
docker info
docker version
```
As a first program, you can run the "Hello-World" routine:
```
sudo docker run hello-world
```
If successful, it will prompt "Hello World" to your command line.
Afterwards, enable your user "pi" to run docker containers on your Raspberry:
```
sudo usermod -aG docker pi
```

If you want to install other images then the described below, you can find them on [hub.docker.com](https://hub.docker.com/). Just browse for the application you need, it will always show the command you need to use to pull the image to your Raspberry. Copy the command to the clipboard and insert it into your command line with a right click of your mouse.

### Portainer
To organise your docker containers and images (e.g. start / stop and create / delete some), you can use Portainer. It will come with a graphical interface available via your Web Browser. It will already run in a container itself, so it can be pulled from Docker:
```
sudo docker pull portainer/portainer:linux-arm
```
Next, it only needs to be started:
```
sudo docker rrun --restart always -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer:linux-arm myportainer
```
That way, it will already be started on startup of your Raspberry, so you don't need to start it from the command line always.

### Eclipse Mosquitto
Mosquitto will be used as a MQTT Broker. Just pull the image from Docker:
```
sudo docker pull eclipse-mosquitto
```

For some reason, the suggested way from Docker-Hub did not work for Mosquitto, so I found a workaround:
* First, you need to create some directories for your Mosquitto manually:
```
mkdir mosquitto
mkdir /mosquitto/config
mkdir /mosquitto/data/
mkdir /mosquitto/log/
```
* Then, create a file called `<mosquitto.conf>` inside the directory `</mosquitto/config>` This can be done using the nano-editor in the following way:
```
nano /mosquitto/config/mosquitto.conf
```
Then copy the following code lines into the file and save it with `<CTRL>`+ `<X>`:
```bash
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
```
Afterwards, you can run the container for the first time:
```
sudo docker run -it -p 1883:1883 -p9001:9001 -v /home/pi/mosquitto/config/mosquitto.conf:/mosquitto/config/mosquitto.conf eclipse-mosquitto
```

### Influx DB
As a database for our monitored sensor nodes, we use Inflxu DB as it easily handles data which updates regulary and should be displayed over the time. 
Again pull the image:
```
sudo docker pull influxdb
```
Afterwards run it:
```
sudo docker run -p 8086:8086 -v influxdb:/var/lib/influxdb influxdb
```

### Grafana
Grafana will be used to quickly build fancy looking dashboards and connect to the Influx DB. Pull the image:
```
sudo docker pull grafana/grafana
```
Afterwards, run it like the others:
```
sudo docker run -d --name=grafana -p 3000:3000 grafana/grafana mygrafana
```

### Python 3
Python will be used for some background services to communicate with the GPIO pins and sensors (which then could be used independently of Node-RED):
```
sudo apt-get python3
sudo apt-get install python3-setuptools
sudo apt-get install python3-pip
pip3 install --upgrade setuptools
```

To access the Pins, download the Python libraries:
```
sudo apt-get install pigpio python-pigpio python3-pigpio
```

#Library and test script for the DHT sensors
To test the DHT connection and function as well as giving the option to use it with Python rather then Node-RED, download the libraries:
```
pip3 install adafruit-circuitpython-dht
sudo apt-get install libgpiod2
```
Afterwards, we will create a simpe script for testing:
```
nano dht_simpletest.py
```
Insert the content from the `<dht_simpletest.py>` script in the repository. Save it with `<CTRL>`+`<X>`. Then you will be able to run the script:
```
python3 dht_simpletest.py
```



