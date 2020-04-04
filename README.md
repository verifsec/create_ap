## Features
* Create an AP (Access Point) at any channel.
* Choose one of the following encryptions: WPA, WPA2, WPA/WPA2, Open, **WEP**, **WPA3**(SAE/Enhanced Open).
* Choose one of the folowing modes: **TKIP** or **CCMP**
* Hide your SSID.
* Disable communication between clients (client isolation).
* IEEE 802.11n & 802.11ac support
* Internet sharing methods: NATed or Bridged or None (no Internet sharing).
* Choose the AP Gateway IP (only for 'NATed' and 'None' Internet sharing methods).
* You can create an AP with the same interface you are getting your Internet connection.
* You can pass your SSID and password through pipe or through arguments (see examples).


## Dependencies
### General
* bash (to run this script)
* util-linux (for getopt)
* procps or procps-ng
* hostapd
* iproute2
* iw
* iwconfig (you only need this if 'iw' can not recognize your adapter)
* haveged (optional)

### For 'NATed' or 'None' Internet sharing method
* dnsmasq
* iptables


## Installation
### Generic
    git clone https://github.com/verifsec/create_ap
    cd create_ap
    make install

## Examples
### No passphrase (open network):
    create_ap wlan0 eth0 MyAccessPoint

### No passphrase but encryption (Enhanced Open)
    create_ap --enhanced_open -x /path/to/hostapd wlan0 eth0 MyAccessPoint
    (Note: You have better use hostapd 2.7 <=. See *Build hostapd for WPA3*.)

### WEP key:
    create_ap --wep wlan0 eth0 MyAccessPoint MyPassPhrase

### WPA + WPA2 passphrase:
    create_ap wlan0 eth0 MyAccessPoint MyPassPhrase

### WPA-TKIP:
    create_ap -w 1 --tkip wlan0 eth0 MyAccessPoint MyPassPhrase
    (Note: Realtek drivers usually have problems with WPA1, forcing WPA2)

### WPA2-CCMP:
    create_ap -w 2 --ccmp wlan0 eth0 MyAccessPoint MyPassPhrase

### WPA3 (SAE Dragonfly Handshake):
    create_ap --sae --x /path/to/hostapd wlan0 eth0 MyAccessPoint MyPassPhrase
    (Note: You have better use hostapd 2.7 <=. See *Build hostapd for WPA3*.)

### AP without Internet sharing:
    create_ap -n wlan0 MyAccessPoint MyPassPhrase

### Bridged Internet sharing:
    create_ap -m bridge wlan0 eth0 MyAccessPoint MyPassPhrase

### Bridged Internet sharing (pre-configured bridge interface):
    create_ap -m bridge wlan0 br0 MyAccessPoint MyPassPhrase

### Internet sharing from the same WiFi interface:
    create_ap wlan0 wlan0 MyAccessPoint MyPassPhrase

### Choose a different WiFi adapter driver
    create_ap --driver rtl871xdrv wlan0 eth0 MyAccessPoint MyPassPhrase

### No passphrase (open network) using pipe:
    echo -e "MyAccessPoint" | create_ap wlan0 eth0

### WPA + WPA2 passphrase using pipe:
    echo -e "MyAccessPoint\nMyPassPhrase" | create_ap wlan0 eth0

### Enable IEEE 802.11n
    create_ap --ieee80211n --ht_capab '[HT40+]' wlan0 eth0 MyAccessPoint MyPassPhrase

### Client Isolation:
    create_ap --isolate-clients wlan0 eth0 MyAccessPoint MyPassPhrase

## Systemd service
Using the persistent [systemd](https://wiki.archlinux.org/index.php/systemd#Basic_systemctl_usage) service
### Start service immediately:
    systemctl start create_ap

### Start on boot:
    systemctl enable create_ap


## Build hosapd for WPA3
   apt install pkg-config libnl-3-dev libssl-dev libnl-genl-3-dev
   wget https://w1.fi/releases/hostapd-2.7.tar.gz  # for example
   tar xvzf ./hostapd-2.7.tar.gz
   cd ./hostapd-2.7/hostapd/
   echo -ne "\nCONFIG_IEEE80211W=y\nCONFIG_DPP=y\nCONFIG_SAE=y\nCONFIG_SUITEB=y" >> ./defconfig
   cp ./defconfig .config
   make -j 2

## License
FreeBSD
