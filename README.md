
# Taiyo

Project mashup for:

- Manager (Sonarr)
- Indexing (Jackett)
- Torrenting (Transmission)
- Viewing (Jellyfin)
- With VPN support for transmission (Gluetun)

  

## Usage

For linux, configured for debian-based systems.

Tested for Raspberry PI 3, running [dietpi](https://dietpi.com/).

  

### Step 1: Installing Sonarr

**For Dietpi users:** Can be installed with the dietpi-software utility.

Else, install sonarr with the command:
```
curl -o- https://raw.githubusercontent.com/Sonarr/Sonarr/develop/distribution/debian/install.sh | bash
```
When asked to set a user and group for linux permissions, take note of them, as they will be used in a later step.

Check that it works (may require a restart or a daemon reload) with ``http://localhost:8989`` (or replace localhost with server local ip address).

### Step 2: Installing Jellyfin
**For Dietpi users:** Can be installed with the dietpi-software utility.

Else, install jellyfin with the command:
```
curl https://repo.jellyfin.org/install-debuntu.sh | sudo bash
```

Both sonarr and jellyfin can be managed with ``systemctl`` utility.

### Step 3: Install Taiyo Docker Image
Taiyo uses existing projects to accomplish functionality.  
- *OPTIONAL(Recommended):* **Gluetun for VPN**
For VPN support, I utilize gluetun open-source project that supports many VPN service providers. The provided configuration is for [Mullvad](https://mullvad.net/), however you can choose the provider you want.
**Gluetun** is used as a network container, as all traffic of the torrenting container is routed through it. It can be skipped, but it is not recommended.
**Configuration:** You can configure Gluetun inside the ``docker-compose.yml`` file.
- **Transmission for torrenting**
**Transmission** is installed as a separate docker container. After configured, **it must be configured to work with sonarr** from the sonarr interface.
**Configuration:** You can configure Gluetun inside the ``docker-compose.yml`` file.

**Running the Image**
Run the command:
```
docker-compose up
```
in the directory of the docker-compose.yml file. To detatch from the terminal, you can use command:
```
docker-compose up -d
```

### Step 4: Install Jackett
**For Dietpi users:** Can be installed with the dietpi-software utility.
Else, install jacket with the command:
```
cd /opt && f=Jackett.Binaries.LinuxAMDx64.tar.gz && release=$(wget -q https://github.com/Jackett/Jackett/releases/latest -O - | grep "title>Release" | cut -d " " -f 4) && sudo wget -Nc https://github.com/Jackett/Jackett/releases/download/$release/"$f" && sudo tar -xzf "$f" && sudo rm -f "$f" && cd Jackett* && sudo ./install_service_systemd.sh && systemctl status jackett.service && cd - && echo -e "\nVisit http://127.0.0.1:9117"
```
Verify that it is working by navigating to ``http://localhost:9117``, or replace ``localhost`` with the local ip address of the server.

**Configuration:**
- **Linking with Sonarr:** Follow the instructions in the Jackett interface to connect it with Sonarr.  
- **Adding Indexers:** You need to add your selection of indexers through Jackett using the interface. After that, they will appear in the Sonarr indexers list (Settings > Indexers).

### Step 5: Configure Sonarr
Open the Sonarr interface from a browser (``http://localhost:8989``).   
Go to Settings > Download Clients and create a new download client configuration. Select **transmission** and enter a name. After that, press **test** and then **save**.


## Useful Ports
Sonarr: ``http://localhost:8989``
Transmission: ``http://localhost:9091``
Jackett: ``http://localhost:9117``
Jellyfin: ``http://localhost:8097``