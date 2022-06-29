# omnetWireless
The repo contains omnetpp code to connect an other program with omnetpp (via udp) and send messages wirelessly inside omnetpp all in realtime.

In order to make this work you must first install Omnet++ (https://omnetpp.org/) and also INET (https://inet.omnetpp.org/Installation.html).
In this project I used Omnet 6.0 and INET 4.4.0.

Once installed you should create a new Omnetproject. I called mine Ardusim because this is part of a bigger project where I want to let our UAV simulator Ardusim (https://github.com/GRCDEV/ArduSim) work with Omnet++

In that project there are various modules:
1. RealTime => this module makes sure that the omnet simulation is running in realtime. It also opens a UDP port, but I am not using that one.
2. There are some other modules in the network: visualizer, configuratior and a radio. These are necessary for the wireless comunciation but just standard INET nodes so I will not go into detail.
3. host => this host is an AdhocHost (one of the available hosts in INET). It runs a custom application (set in omnetpp.ini) which is called DroneApp

The code of the droneapp is placed inside inet folder (inet.src.inet.applications.udpapp.costomApp)
In order to make this repo small I did not include whole INET so you will have to copy the costomApp folder to that location.

After compiling the code you should be able to run it. Nothing will happen though.
In the code I am waiting for a message to arrive in one of the hosts. They are listining for UDP packages starting at port 5000.
Each host has it own port so host 0 has port 5000 and host 1 has port 5001. 

Once they receive this message they will send it wirelessly (using 802.11p) to another node. 
In my example I have only two node so host 0 always sends it to host 1 and vica versa. 
This behaviour is coded in costomApp.DroneApp::handleMessageWhenUp.
The other node will receive this message modify it slightly and then send it out via UDP starting at port 6000 (again host 0 port 6000 host 1 port 6001).

Important is to know that in the function costomApp.DroneApp::handleMessageWhenUp I am using a selfmessage to trigger that function.
The selfmessage arrives every 0.5 seconds. This means that your service which is sending message to Omnetpp cannot send it faster then that (otherwise, it will never get in the else block and thus never send out the messages). For me this frequency was good enough so I didn't change it, but I believe you can simply change the 0.5 seconds to your needs. Another caviat is that, while using the GUI rendering the graphics takes up to much time, and thus the application will not longer be running in real time. The easy solution is to run in using the commandline interface. Which can be changed in the run configurations (change Qtenv to Cmdenv).
