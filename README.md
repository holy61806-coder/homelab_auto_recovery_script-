# homelab-auto-recovery
A simple Linux auto-shutdown script that monitors LAN/gateway connectivity and safely shuts down the servers during long power outages and i have used a raspberry pi zero w to autorecover the homelab once the electricity returns and the Pi boots, it automatically sends Wake-on-LAN (WOL) packets to restart the homelab systems.

# Overview 
The idea is simple:

Linux servers monitor network/gateway availability.
During a prolonged outage, the servers safely shut themselves down.
A Raspberry Pi Zero W stays connected to power.
When electricity returns, the Pi boots automatically.
The Pi sends Wake-on-LAN packets to start all homelab machines again.

The Raspberry Pi is not responsible for shutting systems down. Each machine handles its own shutdown process locally using its own monitoring script.

The Pi is only used for:

automatic recovery
Wake-on-LAN automation
retrying boot packets after outages
