Quick Install
 

HOMER Setup
This document provides guidance, packages and details to get HOMER installed & running

HOMER 10 (beta)
🐋 Docker Install (easiest)
HOMER 7
🎁 Manual Install (easy)
📑 Quick Install (easier)
🐋 Docker Install (easiest)
Clients & Integrations
📞 HEP Capture Agents
📈 HEP Grafana Integration
Found an problem with this guide? Please open an issue and help us improve!


🎁 Manual Install:
Homer and its components can easily be installed manually in just minutes.

Debian
Install the sipcapture repository for Debian/Ubuntu:

curl -s https://packagecloud.io/install/repositories/qxip/sipcapture/script.deb.sh | sudo bash
Install the latest package

HOMER/HEP Agent
apt install heplify
HOMER/HEP Server
apt install heplify-server
apt install homer-app
CentOS
Install the sipcapture repository for CentOS/EL 7:

curl -s https://packagecloud.io/install/repositories/qxip/sipcapture/script.rpm.sh | sudo bash
HOMER/HEP Agent
yum install heplify
HOMER/HEP Server
yum install heplify-server
yum install homer-app
🔧 Configuration
Configure Homer Capture Server /opt/heplify-server/heplify-server.toml
Example Configuration
Configure DB connectors
Postgres
Loki (optional)
Prometheus Metrics Endpoint
Configure Homer Application /usr/local/homer/etc/webapp_config.json
Example Configuration
Configure DB connectors
Postgres
Prometheus API (optional)
InfluxDB API (optional)
Loki API (optional)
⏳ Initialization
Adjust with your database credentials, and execute the following commands to setup the backend components:

Create Homer DBs
homer-app -create-config-db -database-root-user=postgres -database-host=localhost -database-root-password=postgres -database-homer-user=homer_user
homer-app -create-data-db -database-root-user=postgres -database-host=localhost -database-root-password=postgres -database-homer-user=homer_user
Create Tables, Populate defaults, Upgrade
homer-app -create-table-db-config 
homer-app -populate-table-db-config 
homer-app -upgrade-table-db-config 
🔖 Start Services
systemctl start heplify-server
systemctl start homer-app
Done!
You should now be able to access your HOMER instance via HTTP on port 9080 and send HEPv3 traffic to port 9060/UDP or 9061/TCP (note ports can be modified by the configuration)

image



📑 Quick Install:
A script is provided to deploy the baseline elements with minimal interaction on a vanilla server: https://github.com/sipcapture/homer-installer

Quick install currently supports modern debian and centos based operating systems.

Instructions
Make sure the script is executed as root on a netinstall vanilla server:

for Debian

apt-get install libluajit-5.1-common libluajit-5.1-dev lsb-release wget curl git
For CentOS

yum install redhat-lsb-core wget curl git
then Download Homer Installer script

wget https://github.com/sipcapture/homer-installer/raw/master/homer_installer.sh
chmod +x homer_installer.sh
./homer_installer.sh

🐋 Docker Install:
A set of docker-compose bundles is provided to bootstrap a full Homer 7.7 deployment including optional elements: https://github.com/sipcapture/homer7-docker

Instructions
This procedure requires docker and docker-compose installed on the target system.

Deployment
git clone https://github.com/sipcapture/homer7-docker
cd homer7-docker/heplify-server/hom7-prom-all
docker-compose up -d
Access & Usage
Homer:9080 (admin/sipcapture)
Grafana:9030 (admin/admin)
Prometheus:9090 (admin/admin)
Loki:3100 (admin/admin)
Alertmanager:9093 (admin/admin)
Done!
You should now be able to access your HOMER instance via HTTP on port 9080 and send HEPv3 traffic to port 9060/UDP or 9061/TCP (note ports can be modified by the docker-compose configuration)

image


📞 Capture Agents
Ready to ship data to Homer? Install a HEP Capture Agent based on your needs and preferences:

HEPlify: CA developed in go, portable, near zero configuration

CaptAgent: CA developed in C, ideal for complex configurations

Native: Native HEP Agents are available in OpenSIPS, Kamailio, Asterisk, Freeswitch, RTP:Engine and many more. Consult the Wiki to get specific examples for your platform.

Event Agents: HOMER can collect, index and correlate non-packet events such as Logs, RTC stats, CDRs, and more using HEP supported by a variety of tools such as paStash and Telegraf


📈 Grafana Integration
Deploying HOMER alongside Grafana?

Import the HOMER Preset Dashboards & Widgets to get started with standard KPIs in seconds.

