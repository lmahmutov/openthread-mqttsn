# Thread MQTT-SN Client Library
This repository contains MQTT-SN client implementation based on OpenThread SDK. Protocol communicates over UDP in Thread mesh network. 

For development purposes and better interoperability the repository contains terporarily whole project created by [MCUXpresso IDE](https://www.nxp.com/support/developer-resources/software-development-tools/mcuxpresso-software-and-tools/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE).

The project is in very early stage of development, it may change over time. Documentation and example Docker images will be released in January 2019. Library will be integrated into OT CLI example in the future.

## Examples

## Demonstration
There are prepared public [Docker](https://www.docker.com/) images which can be used for MQTT-SN communication demonstration. Images are built for ARM architecture and tested on [Raspberry Pi 3](https://www.raspberrypi.org/) platform.

### Prerequisities
You should have installed Docker CE environment. Please follow [official guide](https://docs.docker.com/install/) for product installation and configuration.

Create new network ``test`` with IP address ``172.18.0.0`` so specific IP addresses can be set to individual images.
```
sudo docker network create --subnet=172.18.0.0/16 test
```

### Obtain and run demonstration images
#### kyberpunk/border-router
Image contains custom build of [OpenThread Border Router](https://github.com/openthread/borderrouter). OpenThread official image can be used instead. Image must run on computer with connected Thread NCP device. You can find documentation and more information on [project website](https://openthread.io/guides/border-router/docker/run).

```
# Download OTBR image
sudo docker pull kyberpunk/border-router

# Run OTBR container
sudo docker run -d --name otbr --sysctl "net.ipv6.conf.all.disable_ipv6=0 \
        net.ipv4.conf.all.forwarding=1 net.ipv6.conf.all.forwarding=1" \
        -p 80:80 --dns=127.0.0.1 -it --volume \
        /dev/ttyACM0:/dev/ttyACM0 --net test --ip 172.18.0.6 \
        --privileged kyberpunk/border-router \
        --ncp-path /dev/ttyACM0 --nat64-prefix "2018:ff9b::/96"
```
NAT64 network prefix is set to ``2018:ff9b::/96`` because default prefix cannot be applied on devices in private network. Container is started in network ``test`` with IP address ``172.18.0.6``. You should set ``--ncp-path`` parameter accordingly to your host platform.

#### kyberpunk/mosquitto
Image contains custom build of [Eclipse Mosquitto](https://mosquitto.org/) MQTT broker. For demonstration purposes is broker configured to use no authentication and no security. Broker is listening on standard port 1883 which is also bound to host machine network interfaces. Container IP address in Docker network is set to ``172.18.0.7``.

```
# Download OTBR image
sudo docker pull kyberpunk/mosquitto

# Run OTBR container
sudo docker run -d --name mosquitto --net test --ip 172.18.0.7 --privileged -p 1883:1883 kyberpunk.azurecr.io/mosquitto
```


## References
* [OpenThread GitHub](https://github.com/openthread/openthread)
* [OpenThread Border Router GitHub](https://github.com/openthread/borderrouter)
* [MQTT-SN Specification](http://mqtt.org/new/wp-content/uploads/2009/06/MQTT-SN_spec_v1.2.pdf)
* [Eclipse Paho MQTT-SN serialization and gateway](https://github.com/eclipse/paho.mqtt-sn.embedded-c)
* [NXP Kinetis SDK](https://www.nxp.com/support/developer-resources/evaluation-and-development-boards/freedom-development-boards/mcu-boards/software-development-kit-for-kinetis-mcus:KINETIS-SDK)
