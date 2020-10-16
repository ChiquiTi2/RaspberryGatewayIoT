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
