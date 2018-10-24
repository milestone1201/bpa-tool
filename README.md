# Threat Visualization:

Docker automation for showing different views of PAN-OS threat logs




**USE:**

In already deployed scenarios, where customers can't use the iron-skillet tool, 
this helps to provide visualizations to quickly and effectively understand what is happening
and to be able to safely tune threat profiles to blocking mode. Using along side the Palo
Alto Networks BPA tool may prove to be useful. 




### Pre-requisite - Docker For Mac:

Installation guide:  https://docs.docker.com/docker-for-mac/install/

Download link:  https://store.docker.com/editions/community/docker-ce-desktop-mac

Once installed it is recommended that you increase the amount of RAM available to docker. I have mine set to 8 GB.

https://stackoverflow.com/questions/32834082/how-to-increase-docker-machine-memory-mac/ 




### Directory Contents:

    .
    ├── AUTHORS
    ├── LICENSE
    ├── README.md
    ├── configs
    │   └── logstash-threat.conf        <======= #2
    ├── data
    │   └── threat-data.csv             <======= #4
    ├── docker-compose.yaml             <======= #1
    ├── images
    │   ├── c2.png
    │   ├── ips.png
    │   ├── malware.png
    │   └── overview.png
    └── stuff
        └── kibana.json                 <======= #3


**#1 /bpa-viz/docker-compose.yaml** - creates the container along with configuration

**#2 /bpa-viz/configs/logstash-threat.config** - configures logstash to parse a raw CSV dump from  PAN-OS threat logs

**#3 /bpa-viz/stuff/kibana.json** - imported into kibana to populate indecies and visulaizations

**#4 /bpa-viz/data/threat-data.csv** - (demo data, you will replace) csv export of threat logs from PAN-OS (**must stay as threat-data.csv and be placed in the data folder**)



### Deploy Container:

Start by obtaining a raw CSV dump from a firewall running PAN-OS (testing done on device running 8.1.X). Once you have the file make sure to save it in the **/data** 
directory, with the file named ***threat-data.csv***

Navigate to ***/bpa-viz*** and run:

```bash
> docker pull sebp/elk:642
```

***NOTE:*** this only needs to be done the first time you use this tool, after the first time follow the remaining directions.

Stay in **/bpa-viz**, once the download is done, run the following command to start your container

```bash
> docker-compose up
```

This starts the container, places the threat-data.csv and logstash-threat.conf in correct locations, and begins processing the data

You may notice some http 400 responses once the container is fully deployed and logstash has started parsing the messages. Give this a little time once the log messages stop appearing on your
CLI. After that navigate your browser to: http://localhost:5601 (Kibana GUI)



### Configure Kibana:

In Kibana GUI, navigate to the left hand side and select the **management** link.

Go to **Kibana > Saved Objects > Import**

Import the **/bpa-viz/stuff/kibana.json** file

Navigate to the **dashboard** link on the left hand side of the Kibana GUI.

If you look in the upper right hand corner your time defaults to the last 15 minutes. This may not work well on the data set you have imported.
Change the time frame to the last 90 days (or something that fits your use). You can also enable the auto-refresh feature (lets you monitor data processing into Kibana)
because your data is likely being processed if you have moved quickly through these directions.



### Contribute:

You can view in Kibana how the dashboard was created along with the individual visualizations. If you come up with new and interesting ways
to view the data, please submit them via a pull request so that I can include them in the project for everyone to consume.



### Clean Up:

Once you have done a **control-c** at your CLI and closed the container, you can easily remove artifacts while keeping the deployment time
minimal on the next run with the following command.

```bash
> docker-compose down
```

This removes artifacts to save space and doesn't slow down future deployments of the solution.



### Thanks:
To **sebp** for the docker build files:  https://hub.docker.com/r/sebp/elk/


### Screenshots:

![Alt text](./images/overview.png?raw=true "overview")

![Alt text](./images/malware.png?raw=true "malware")

![Alt text](./images/c2.png?raw=true "c2 channels")

![Alt text](./images/ips.png?raw=true "ips/ids")