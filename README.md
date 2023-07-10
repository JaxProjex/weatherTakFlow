# weatherTakFlow
Node-Red Flow to forward Open Meteo Weather Data from "Weather" CoT marker to ATAK via multicast or TAKServer. (used in conjunction with Ampledatas node-red-contrib-tak).

![flow](/weatherTakFlow.png?raw=true "Node Red Flow")

REQUIREMENTS:

-Node-Red Server

-node-red-contrib-tak https://github.com/snstac/node-red-contrib-tak

-TAKServer (or Multicast supported devices)

------------------------------

SETUP NODE-RED:

-https://nodered.org/docs/getting-started/

-options include installing node-red on your local PC, a Raspberry Pi or similar single board computer or mini PC, deploying node-red on a cloud server or virtual private server (VPS), Docker Container, or local virtual environment. After installing node-red you should be able to go to the node-red dashboard at http://*nodeRedIPaddress:1880 you may have to open appropriate ports (1880) to allow devices to access the node-red dashboard.

-ensure you install the "node-red-contrib-tak" node. If not there should be a prompt when you import weatherTakFlow in Node-Red that there are nodes that need to be installed and will automatically install them for you if you allow. In the event that isnt the case, in Node-Red: Menu (3 horizontal lines) > Manage palette > Install > Search "node-red-contrib-tak" > Install > Install

------------------------------

IMPORT .JSON FLOW TO NODE RED:

-in GitHub: click on "weatherTakFlow.json" > click on the download icon "Download raw file" > note where the "weatherTakFlow.json" file downloaded to, default is in your Downloads folder.

-in Node-Red: click on menu icon (3 horizontal lines top right) > click on "Import" > click on "select a file to import" > go to Downloads folder and click on "weatherTakFlow.json" > Upload > Import

ALTERNATIVELY..

-you can just copy the whole "weatherTakFlow.json" code from GitHub and paste it into the Node-Red Import Clipboard.

------------------------------

CONFIGURE TAKSERVER:

-TAKServer can be hosted on various platforms: Raspberry Pi, Mini PCs, your local PC, Cloud Servers or Virtual Private Servers (VPS), Docker Containers, or Virtual Environments. Regardless, if you're looking for instructions on setting up your TAKServer I recommend ATAKHQs guides https://www.atakhq.com/en/tak-server.

-Configuring the TCP (TAKServer) node in Node-Red weatherTakFlow, Input your TAKServer IP and Port, and checkbox whether you're using SSL/TLS or not. If you are using SSL/TLS, ensure you upload you TAKServer certificates and key that are located in the directory of your TAKServer that stores all client certificates/keys (ie: ubuntu docker container is "/opt/tak/certs/files"). I Suggest creating a TAK client "node-red" to be used specifically for Node-Reds handling of forwarding data to TAK. In your certificates file you will want to copy and upload "node-red.pem" as the "Certificate", "node-red.key" as the "Private Key" and "ca.pem" as the "CA Certificate" in the TLS Configuration Properties in the TCP (TAKServer) node in Node-Red. You will also need to enter the Passphrase for your TAKServer truststore, this can be found in your TAKServers CoreConfig.xml file. Default is "atakatak".

------------------------------

USING THE WEATHERTAKFLOW:

-once your TCP TAKServer node is connected, you're ready to start having your TAK devices get cell and wifi data.

-in ATAK: Tool Bar (top) > Waypoint icon > *choose any CoT 2525C icon (others havent been tested) > *tap on map of location to request Weather Data from > *rename CoT icon as "weather" or "Weather" or "WEATHER" (without the quotes) > Send > Broadcast. weatherTakFlow will then resend the CoT marker with a weather icon that best reflects the weather data in the next 24 hours along with weather data placed in the remarks section of the CoT marker. 

-in iTAK: Tool Bar (bottom) > *long press on map location to request weather data from > *rename CoT icon as "weather" or "Weather" or "WEATHER" (without the quotes) > Share > Share with Contacts > Share With All. weatherTakFlow will then resend the CoT marker with a weather icon that best reflects the weather data in the next 24 hours along with weather data placed in the remarks section of the CoT marker. Remarks can be found by clicking on the CoT marker > Edit > Remarks.

-------------------------------

HOW THE WEATHERTAKFLOW WORKS:

-TCP (TAKServer) / UDP Multicast node sends CoT traffic to TAK node to turn into a readable JSON format.

-TAK node sends CoT as JSON to filter data node.

-filter data node constantly reads and waits til a CoT marker is "weather" or "Weather" or "WEATHER" as the callsign in which it grabs the plotted location to request weather data for.

-http req nodes send a http GET to request weather data from open-meteo.

-json node formats the http weather response (this is dumb because i couldve changed the http output but oh well) to be joined with the previous info from the original CoT (uid, lat, lon).

-weather data cot then formats a json CoT with the same lat/lon and uid but with new icon and weather data response added in remarks to then get passed to TAK node

-TAK turns the json data to CoT and pushes it via TCP (TAKServer) or UDP Multicast depending on what is chosen.

--------------------------------

TROUBLESHOOTING / KNOWN ISSUES:

-in the event the Node-Red service explodes and you lose connection to the Node-Red UI and cant reconnect, you may need to restart your Node-Red service

$ sudo service node-red restart
