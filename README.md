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


## Auto Shutdown Script

This script continuously monitors gateway/LAN availability. If the gateway becomes unreachable for a prolonged period, the server safely shuts itself down to avoid filesystem corruption or unclean shutdowns during power outages.

### shutdown_monitor.sh

```bash
#!/bin/bash

GATEWAY=" gateway ip "
CHECK_INTERVAL=5
MAX_FAILURES=6

FAILURES=0

echo "Starting gateway monitor..."

while true; do
    if ping -c 1 -W 2 "$GATEWAY" > /dev/null; then
        FAILURES=0
    else
        FAILURES=$((FAILURES + 1))
        echo "Gateway unreachable ($FAILURES/$MAX_FAILURES)"
    fi

    if [ "$FAILURES" -ge "$MAX_FAILURES" ]; then
        echo "Power outage detected. Initiating safe shutdown..."
        shutdown -h now
        exit 0
    fi

    sleep "$CHECK_INTERVAL"
done
```

---

## Background Service (systemd)

This service runs the monitoring script automatically in the background after every boot.

### power-monitor.service

```ini
[Unit]
Description=Gateway Power Loss Monitor
After=network.target

[Service]
ExecStart=/usr/local/bin/shutdown_monitor.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Installation

```bash
sudo cp shutdown_monitor.sh /usr/local/bin/
sudo chmod +x /usr/local/bin/shutdown_monitor.sh

sudo cp power-monitor.service /etc/systemd/system/

sudo systemctl daemon-reload
sudo systemctl enable power-monitor.service
sudo systemctl start power-monitor.service
```

