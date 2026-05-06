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

# Main purpose 
I wanted:

- automatic homelab recovery after outages
- protection against filesystem corruption
- a low-power always-on controller
- a cheap alternative to enterprise recovery systems
- something simple enough to understand and maintain myself

The Raspberry Pi Zero W turned out to be perfect for this because:

- very low power consumption
- boots quickly
- cheap
- enough for lightweight automation tasks
- can run 24/7 without issues

# how it works 
During Normal Operation :
- All servers run normally.
- The Pi Zero W remains powered on.
- The Pi waits quietly in the background.

During Power Outage
- Servers detect gateway/network loss.
- Local shutdown scripts start running.
- Servers safely shut themselves down.

This helps avoid:

- corrupted filesystems
- unclean shutdowns
- damaged containers/VMs
- failed RAID rebuilds

When Electricity Returns
- The Raspberry Pi Zero W automatically boots.
- A startup service launches the WOL script.
- The Pi sends Wake-on-LAN packets.
- Homelab systems power back on automatically.

The script retries multiple times until systems respond.
