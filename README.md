# Mayaguez One Bus Away Documentation

OBA installation
- This was a research conducted in affiliation with the National Institute of Congestion Reduction (NICR) and the University of South FLorida (USF)
- The team members incorporated in the One Bus Away impelmentation at Mayaguez were:
	- Didier M. Valdes
	- Alberto M. Figueroa Medina
	- Ivette Cruzado
	- Juan Martinez
	- Joshua Santiago
	- Alonso Velasquez
## 1. Introduction
---

The software implemented on this repository was modified from their respective original source repositories. This repository function is to inform of how the installation of the development software was conducted. For the totality of the researcg, the following software was implemented:
- Server hosting
	- Digital Ocean | [link](https://www.digitalocean.com/)
- Server secutiry
	- Apache | [documentation](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-20-04)
	- SSL certification | [documentation](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu)
- Open source projects used:
	- One Bus Away | [repository](https://github.com/open-austin/onebusaway-docker)
	- Transit Clock | [repository](https://github.com/TheTransitClock/transitclockDocker)
	- Traccar | [repository](https://github.com/traccar/traccar-docker)

## 2. Getting Started
---
Inside this repository are the implemented versions of OBA, Transit Clock and Traccar. To run the implemented software a Linux server was used. The following specifications for a server were used to test the deployments.

| OS Image | Ubuntu >= 22.04   |
| -------- | ----------------- |
| CPU      | 2 GB / 2 AMD CPUs |
| SSD      | 60 GB / NVMe SSD  |
| transfer | 3 TB              |

### 2.1. OBA Installation
---
The following instrcutions explain the strcutre and the installation of the project.

```
File Structure:
	bundle/
	Dockerfile
	gfts.zip
	oba.war
```

| File/Folder | About                                           |
| ----------- | ----------------------------------------------- |
| bundle      | folder holding the processed GTFS               |
| Dockerfile  | Instructions of commands for the service to run |
| gtfs.zip    | GTFS bundle of the Mayaguez Transit System      |
| oba.war     | One Bus Away rest api Java Executable           |
The previos table specifies what each file or folder is related to under the One Bus Away setup

| Env Variables                  | About                                       |
| ------------------------------ | ------------------------------------------- |
| GTFS_REALTIME_TRIP_URL         | API key from Transit Clock trip updates     |
| GTFS_RELTIME_VEHICLE_POSITION  | API key from Transit Clock vehicle position |
| GTFS_REALTIME_ALERTS(optional) | API key for Alerts                          |
The previos table specifies the variables needed to run One Bus Away using this code. The variables needed can be changed on 'OBA-UPRM-v/Dockerfile'.

### Commands
1. Generate bundle from GTFS.zip
```
java -Xmx1G -server   -jar oba.war -build /root/OBA-v1/save_oba_gtfs_uprm/gtfs.zip  /root/OBA-v1/bundle/
```

2. Build OBA dependencies and run isntance
```
docker build -t oba .
docker run -p 8080:8080 -t oba
```
The previous code has the necesary commands to create a OBA bundle and run the OBA docker instance. To verify if the instance is active reference: http://ip:8080/api/where/agencies-with-coverage.xml?key=TEST. Changing the test api key from TEST is strongly suggested.

### 2.2. Transit Clock Implementation
---
The following instrcutions explain the structure and the installation of the project.

| Env Variables           | About                                                                                                                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| DEVELOPER_TESTING_EMAIL | Email used in 'TClock/transitclock.properties' line 92-93 for email reciepts.                                                                          |
| GTFS_DOWNLOAD_URL       | External internet access to the GTFS and refrence it as a downloadable link. Edit this in 'TClock/Dockerfile' line 6; Edit 'TClock/Dockerfile' line 16 |
| TRACCAR_IP              | IP address from the traccar service. Edit in 'TClock/Dockerfile' line 7; Edit in 'TClock/go.sh' line 15                                                |
| AGENCY_NAME             | Name of the transportation system; Edit in 'TClock/Dockerfile' line 5; Edit in 'TClock/go.sh' line 14                                                  |
| TRACCAR_EMAIL           | Email for traccar admin account. Edit in 'TClock/transitclock.properties' line 85                                                                      |
| TRACCAR_PASSWORD        | Password for traccar admin account. Edit in 'Tclock/transit.properties' line 86                                                                        |
| TRACCAR_URL             | URL from Traccar API. URL is referenced as 'TRACCAR_IP:PORT/api'                                                                                       |
The previous table references the variables that requiere modifications for Transit Clock to run.
### Commands

1. If Docker is not installed run following

```
curl -sSL [https://get.docker.com/](https://get.docker.com/) | sh
```

2. To iniate Transit Clock run the following at the 'TClock' root
```
./go.sh
```


### 2.3. Traccar
---
The following instrcutions follow step by step the documentation provided by Traccar. [link](https://www.traccar.org/install-digitalocean/)
### Commands

1. Install unzip utility and MySQL server
```
apt update && apt -y install unzip mysql-server
```

2. Set database password and create a new database
```
mysql -u root --execute="ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root'; GRANT ALL ON *.* TO 'root'@'localhost' WITH GRANT OPTION; FLUSH PRIVILEGES; CREATE DATABASE traccar;
```

3. Download the latest installer```
```
wget https://www.traccar.org/download/traccar-linux-64-latest.zip
```

4. Unzip traccar
```
unzip traccar-linux-*.zip && ./traccar.run
```

5. Update MySQL config
```
cat > /opt/traccar/conf/traccar.xml << EOF
<?xml version='1.0' encoding='UTF-8'?>

<!DOCTYPE properties SYSTEM 'http://java.sun.com/dtd/properties.dtd'>

<properties>

    <entry key="config.default">./conf/default.xml</entry>

    <entry key='database.driver'>com.mysql.jdbc.Driver</entry>
    <entry key='database.url'>jdbc:mysql://localhost/traccar?zeroDateTimeBehavior=round&amp;serverTimezone=UTC&amp;allowPublicKeyRetrieval=true&amp;useSSL=false&amp;allowMultiQueries=true&amp;autoReconnect=true&amp;useUnicode=yes&amp;characterEncoding=UTF-8&amp;sessionVariables=sql_mode=''</entry>
    <entry key='database.user'>root</entry>
    <entry key='database.password'>root</entry>

</properties>
EOF
```

6. Run Traccar service
```
service traccar start
```
