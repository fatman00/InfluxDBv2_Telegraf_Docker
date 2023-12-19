# InfluxDBv2_Telegraf_Docker
 Run InfluxDB 2.1 and Telegraf 1.22 in containers

 To learn more please read this blog on [Running InfluxDB 2.0 and Telegraf Using Docker](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)

# Server installation
Install an Ubuntu 22.04 with docker as shown in the official documentation: https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

Set up Docker's apt repository.
```# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Install the Docker packages.

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verify installation
```
sudo docker run hello-world
```
# TIG installation
Install the TIG components using this github repo:
```
git clone https://github.com/fatman00/InfluxDBv2_Telegraf_Docker.git
cd InfluxDBv2_Telegraf_Docker
docker-compose up -d
```

# Network device configuration
configure network devices to support netconf-yand and setup a telemetry subscription on CPU and Interface statistics

```
netconf-yang
show platform software yang-management process
```

Configure telemetry subscription.
```
telemetry ietf subscription 1
 encoding encode-kvgpb
 filter xpath /process-cpu-ios-xe-oper:cpu-usage/cpu-utilization/five-seconds
 source-address 198.18.134.49
 stream yang-push
 update-policy periodic 500
 receiver ip address 198.18.133.100 57500 protocol grpc-tcp
 exit
!
telemetry ietf subscription 2
 encoding encode-kvgpb
 filter xpath /interfaces-ios-xe-oper:interfaces/interface/statistics
 source-address 198.18.134.49
 stream yang-push
 update-policy periodic 500
 receiver ip address 198.18.133.100 57500 protocol grpc-tcp
 exit
!
```

