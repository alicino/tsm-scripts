

# Deploying Tableau on Linux

## Presenters:

- Alicino Moura - Product Consultant
- Josh Davis - Senior Tech Support Engineer

----

## Agenda

[TOC]

----

## Log in to Virtual Environment

**User:** LabUser

**Password:** Pa$$w0



-------

## Check System Requirements & Updates

#### Supported Distributions in Tableau Server 2019.2

##### .rpm packages

- RHEL 7.3+
- CentOS 7.3+
- Amazon Linux 2
- Oracle Linux 7.3+

##### .deb packages

- Ubuntu 16.04 LTS
- Ubuntu 18.04 LTS
- Debian 9.0+ 



#### Before Starting

##### Define Authentication Method

- Local, AD, SAML...

##### Firewall

- Port requirements: 
  - 80 or 443 - Gateway (SSL)
  - 8850 - Tableau Service Manager (TSM)
  - 8060 and 8061 - PostgreSQL and backup
  - 8000 to 9000 - Range of Tableau Processes
  - 27000 to 27009 - License Service



#### Check System

Open a terminal and type:

Check processors:

```
lscpu
```

Check memory:

```
free -h
```

Check storage:

```
df -h
```



##### Tableau Server Infrastructure Requirements

| Proof of Concept Requirements | Minimum Production Requirements     |
| ----------------------------- | ----------------------------------- |
| Processor 64-bit              | Processor 64-bit                    |
| 4 cores / 8v-CPU (ex. AWS)    | 8 physical cores, 16v-CPU (ex. AWS) |
| 16 GB system memory           | 32 GB system memory                 |
| 15 GB minimum free disk space | 50 GB minimum free disk space       |

Source:

https://www.tableau.com/products/techspecs



#### ... and Updates!

Type in terminal:

```bash
sudo yum -y update
```

```bash
sudo yum upgrade
```

* Detail: in case of error (sometimes it happens)

`sudo kill -9 6867`



----

## Installation

#### Commands for different Linux distribution

##### rpm command

- For RHEL, CentOS, Oracle or Amazon Linux

```bash
sudo yum install tableau-server-2019-3-0.x86_64.rpm
```



##### deb command

- For Ubuntu and Debian

```bash
sudo gdebi –n tableau-server-2019-3-0_amd64.deb
```



#### First commands

Execute the following commands in your VM's terminal:

```bash
cd Downloads

sudo yum install tableau-server{TAB Key twice to autocomplete}

cd /opt/tableau/tableau_server/packages/scripts{TAB Key twice to autocomplete}

sudo ./initialize-tsm --accepteula
```



#### Pay attention now!

Search for this kind of message after accepting the EULA:



> The TSM administrative web interface (and REST API) is now available at
>
> ****
>
> ##### **https://localhost.localdomain:8850**



Go to your browser and type the URL according the message above.



![](C:\Users\amoura\Documents\1. TC19\Images\1_Browser_hostname2.png)



Sign in user user and password.

```bash
LabUser

Pa$$w0
```



![](C:\Users\amoura\Documents\1. TC19\Images\1_SignIn.png)



#### Important! 



Log off and log on to the terminal again before you configure Tableau Server



![](C:\Users\amoura\Documents\1. TC19\Images\Open_terminal_.png)



#### Activate Tableau Server

Following this sequence and type these commands in your VM's terminal:



#### Register the License Key



```bash
tsm login -u LabUser -p Pa$$w0

tsm licenses activate -t
```

Activating a key (if you have it)

```bash
tsm licenses activate -k TSM-SUPER-KEY
```

<img src="C:\Users\amoura\Documents\1. TC19\Images\2_License_Key.png" style="zoom:80%;" />

#### Register the Tableau Server

Execute this commands to register Tableau Server:

```bash
cd ~/Downloads/server-install-scripts

tsm register --template > registration.json

tsm register --file registration.json
```



<img src="C:\Users\amoura\Documents\1. TC19\Images\3_Register.png" style="zoom:80%;" />



#### Setup Authentication Method

We are considering authentication in local server according the file config.json

![](C:\Users\amoura\Documents\1. TC19\Images\4_Config_authentication_file.png)



Type this command to import server configuration:

```bash
tsm settings import -f config.json
```

Apply the changes

```bash
tsm pending-changes apply
```



<img src="C:\Users\amoura\Documents\1. TC19\Images\4_Setup.png" style="zoom:80%;" />



If you area installing through GUI, wait a second to finish the installation.

If you area installing through command line, go to the next step.

<img src="C:\Users\amoura\Documents\1. TC19\Images\5_Initialize.png" style="zoom:80%;" />

#### Initialize TSM

Execute the command:

```bash
tsm initialize
```



> Initializing the server...
> Job id is '1', timeout is 120 minutes.
> 3% - Validating that there are no pending changes.
> 6% - Generating passwords.
> 10% - Generating search server ssl certificate.
> 13% - Generating Elastic Server SSL certificate.
> 16% - Generating key store.
> 20% - Promoting configuration.
> 23% - Waiting for services to reconfigure.
> 26% - Generating new asset key.
> 30% - Saving asset key.
> 33% - Initializing the topology.
> 36% - Waiting for the maintenance app to start.
> 40% - Initializing the temporary database instance.
> 43% - Starting the temporary database instance.
> 46% - Creating roles and databases.
> 50% - Running migrations.
> 53% - Localizing the default projects.
> 56% - Stopping the temporary database instance.
> 60% - Making the temporary database default.
> 63% - Initializing the next active repository.
> 66% - Enabling services needed for server initialization.
> 70% - Connecting to the Backup/Restore service.
> 73% - Saving asset key metadata.
> 76% - Configuring search server.
> 80% - Connecting to the Backup/Restore service.
> 83% - Initializing Elastic Server.
> 86% - Enabling the services required for indexing.
> 90% - Connecting to Vizportal Maintenance.
> 93% - Rebuilding the search index.
> 96% - Finalizing the initialization.
> 100% - Finalizing the topology.
>
> Server was initialized successfully.



#### Start Tableau Server

```bash
tsm start
```

> Starting service...
> Job id is '2', timeout is 30 minutes.
> Service was started successfully.



#### Create an Admin Account

Execute the command to create:

```bash
tabcmd initialuser --server 'localhost:80' --username 'LabUser' --password 'Pa$$w0'
```



<img src="C:\Users\amoura\Documents\1. TC19\Images\6_Complete.png" style="zoom:80%;" />



## Verify Successful Installation

#### Check if Tableau Server is Running

##### Command Line

Type:

```bash
tsm status
```

The answer may be:

![](C:\Users\amoura\Documents\1. TC19\Images\TSM_Status.png)



Check if the Status: **RUNNING** 



##### Web UI

Go to browser and type the server name and port 8850. Example:

> https://localhost.localdomain:8850/ 

Check STATUS menu.

<img src="C:\Users\amoura\Documents\1. TC19\Images\7_Proccesses.png" style="zoom: 67%;" />



#### tsm or tabcmd command?

##### tsm

TSM is used to manage and configure Tableau Server

Examples:

tsm login -s <server_name> -u <account_name>

tsm maintenance backup -f ts_backup –d

tsm configuration set -k install.component.samples -v false

More in: **https://help.tableau.com/current/server/en-us/tsm.htm** 

##### tabcmd

To automate site administration tasks on Tableau Server site

Examples:

tabcmd delete "Sales_Workbook"

tabcmd delete "Sales_Workbook" -s http://tabserver.mycompany.com -u admin -p mypassword

tabcmd publishsamples -n "Samples" -s localhost --username "LabUser" --password "Pa$$w0"

More in: **https://help.tableau.com/current/server/en-us/tabcmd_cmd.htm**



## Extras

#### Not Running?

**Obliterate Script**

Remove completely Tableau Server on Linux, and delete users and groups created by `initialize-tsm`, all related data, configuration information, and logs

Reference:

https://help.tableau.com/current/server-linux/en-us/remove_tableau.htm



Go to directory:

```bash
cd /opt/tableau/tableau_server/packages/scripts.<version_code>
```

Execute the command:

```bash
sudo ./tableau-server-obliterate –y –y –y -l
```



#### Setup SMTP Service

<img src="C:\Users\amoura\Documents\1. TC19\Images\SMTP_Setup.png" style="zoom:80%;" />



#### Automated Installer

How do I get it?

[https://github.com/tableau/server-install-script-samples/tree/master/linux/automated-installer]()



Install via **yum** or **gdebi**

```bash
sudo ./automated-installer -s secrets -r registration.json -f config.json
--accepteula -v -k SUPER-COOL-LICENSE-KEY tableau-server-2019-2-3.x86_64.rpm
```



#### Local Authentication

At least one Server Administrator authenticating in server

![](C:\Users\amoura\Documents\1. TC19\Images\Server_Administrator.png)



## Would you Like to Download this Content?

Go to:

•https://github.com/alicino/tsm-scripts 

Tableau Official Repository

•https://github.com/tableau/server-install-script-samples 









