# Enterprise for Docker

## Introduction

In **≥ 2.0.0-funcrel**, CAST provides a **.yml file** that is used to install the Console front-end in "**enterprise mode**" on Docker (Linux) or Docker Desktop (Microsoft Windows) as part of the download media available on CAST Extend ([**https://extend.castsoftware.com/#/extension?id=com.castsoftware.aip.console\&version=latest**](https://extend.castsoftware.com/#/extension?id=com.castsoftware.aip.console\&version=latest)):



| If you are not able to use Docker / Docker Desktop, please see [**2.x - Enterprise mode - Installation of AIP Console front-end via Java JAR installers**](https://doc.castsoftware.com/display/AIPCONSOLEDRAFT/2.x+-+Enterprise+mode+-+Installation+of+AIP+Console+front-end+via+Java+JAR+installers) for an alternative enterprise deployment option. |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

## Step 1 - ensure deployment requirements are in place

Ensure that all [**requirements**](https://doc.castsoftware.com/display/AIPCONSOLEDRAFT/Deployment+requirements) are in place.

| <p>This section assumes that Docker/Docker Desktop is <strong>already up an running on your target host server</strong>. Installation of third-party software is out of scope of this documentation, however, some tips can be found in the following third-party documentation:</p><p><strong>Ubuntu</strong> </p><ul><li><a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04"><strong>How To Install Docker on Ubuntu</strong></a></li><li><a href="https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04"><strong>How To Install Docker Compose on Ubuntu</strong></a></li><li><a href="https://linuxize.com/post/how-to-install-and-use-curl-on-ubuntu-20-04/"><strong>How to Install curl on Ubuntu</strong></a></li></ul><p><strong>CentOS</strong></p><ul><li><a href="https://docs.docker.com/engine/install/centos/"><strong>How to Install Docker on CentOS</strong></a></li><li><a href="https://docs.docker.com/compose/install/"><strong>How to install Docker Compose</strong></a></li></ul><p><strong>Microsoft Windows</strong></p><ul><li><a href="https://hub.docker.com/editions/community/docker-ce-desktop-windows/"><strong>Install Docker Desktop on Windows</strong></a></li><li><strong>Containers for Linux</strong> must be enabled:</li></ul><p><img src="https://doc.castsoftware.com/download/attachments/575766693/switch_linux.jpg?version=1&#x26;modificationDate=1649342416047&#x26;api=v2" alt="" data-size="original"></p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

## Step 2 - download and process the installation files

### Linux

Download the extension from CAST Extend using **curl:**

* the command below will download the latest release of Console **** as a ZIP file. If you need to install a different release, ensure you state the specific extension release number, for example [**https://extend.castsoftware.com/#/extension?id=com.castsoftware.aip.console/**](https://extend.castsoftware.com/#/extension?id=com.castsoftware.aip.console/)**2.0.0-funcrel**
* ensure you enter an appropriate **CAST Extend API key** in **\<key>**. See [**CAST Extend website**](https://doc.castsoftware.com/display/EXTEND/CAST+Extend+website#CASTExtendwebsite-profile) or [**Download an extension using CAST Extend API**](https://doc.castsoftware.com/display/EXTEND/Download+an+extension+using+CAST+Extend+API) for more information about this:

| <pre><code>curl -O -J "https://extend.castsoftware.com/api/package/download/com.castsoftware.aip.console" -H "x-nuget-apikey: &#x3C;key>" -H "accept: application/octet-stream"
</code></pre> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Then unpack the ZIP that has been downloaded:

| <pre><code>unzip com.castsoftware.aip.console.&#x3C;version>-funcrel.zip
</code></pre> |
| -------------------------------------------------------------------------------------- |

This location is **where the installation will be run from**. A set of files and folders is produced when the extension is unzipped.

### Microsoft Windows

Download the extension from CAST Extend and unzip it to a working folder anywhere on the server - this location is **where the installation will be run from**. A set of files and folders is produced when the extension is unzipped.

## Step 3 - initial customization of .yml file

Before running the installation, you must customize your deployment to your own environment by editing the following file - **this file is NOT supplied with the installation media and therefore must be created first** in the same location as the supplied `docker-compose.yml` file:

| <pre><code>&#x3C;unpacked_zip>/docker-compose.override.yml
</code></pre> |
| ------------------------------------------------------------------------ |

\


| <p>When you run <code>docker-compose up</code> to run the install and start the relevant Docker containers, the system will search for a file named <code>docker-compose.yml</code> and all configured services, networks, volumes etc to create your Docker stack will be read from this file. If you also additionally have a file named <code>docker-compose.override.yml</code> this will be read as well and used as an override file to complement. It works in the following order:</p><ol><li>All definitions from <code>docker-compose.yml</code> will be used</li><li>All definitions that are also defined in <code>docker-compose.override.yml</code> will automatically overwrite the settings from <code>docker-compose.yml</code></li><li>All definitions only available in <code>docker-compose.override.yml</code> will be added additionally.</li></ol><p><strong>CAST highly recommends that ALL customization</strong> is added to the <code>docker-compose.override.yml</code> since the existing <code>docker-compose.yml</code> is overwritten during any future upgrade process of Console front-end.</p> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### Configure the hostname

You must configure a valid hostname, otherwise your remote Nodes will not be able to locate the services they require. Add the following lines to the `docker-compose.override.yml` file:

| <pre><code>services:  
	aip-service-registry:
    	environment:
        	#  - HOST_HOSTNAME=${HOSTNAME} # HOSTNAME environment variable on Linux
      		#  - HOST_HOSTNAME=${COMPUTERNAME} # HOSTNAME environment variable on Windows
      		- HOST_HOSTNAME=host.docker.internal
      		- DELIVERY_FOLDER=c:\aip-node-data\delivery
      		- DEPLOY_FOLDER=c:\aip-node-data\deploy
      		- SHARED_FOLDER=c:\aip-node-data\common-data
</code></pre> |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

This section governs the hostname used within Docker. By default the hostname will be set to `host.docker.internal` however CAST recommends defining an appropriate hostname that can be accessed across the network. You can either use the hostname defined by your **system's environment variable**, or you can **manually specify it**.

For example, to use the HOSTNAME variable on your Linux instance, uncomment the first line and comment the third:

| <pre><code>- HOST_HOSTNAME=${HOSTNAME} # HOSTNAME environment variable on Linux
# - HOST_HOSTNAME=${COMPUTERNAME} # HOSTNAME environment variable on Windows
# - HOST_HOSTNAME=host.docker.internal
</code></pre> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

To use the HOSTNAME variable on your Windows instance, uncomment the second line and comment the third:

| <pre><code># - HOST_HOSTNAME=${HOSTNAME} # HOSTNAME environment variable on Linux
- HOST_HOSTNAME=${COMPUTERNAME} # HOSTNAME environment variable on Windows
# - HOST_HOSTNAME=host.docker.internal
</code></pre> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

To specify a manual hostname, for example an IP address, change the third line to match your requirement:

| <pre><code># - HOST_HOSTNAME=${HOSTNAME} # HOSTNAME environment variable on Linux
# - HOST_HOSTNAME=${COMPUTERNAME} # HOSTNAME environment variable on Windows
- HOST_HOSTNAME=192.168.200.19
</code></pre> |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

Save the file.

### Configure the shared folder paths for Node data

You must configure the path to the three shared folders (for Node related source code delivery/deployment/misc file):

* Where multiple Nodes will be installed, the paths need to point to a mapped network share drive or use a UNC path of the network share, for example: `\\shared\console\`
* If you are installing Console and only one Node together on one Microsoft Windows server, the paths can point to local paths on the Microsoft Windows server itself, providing enough space is available
* In all circumstances, if you are installing Console on a Linux server, you MUST update the paths, since the default paths provided in the `docker-compose.yml` file use Microsoft Windows syntax

Locate the following lines in the `docker-compose.override.yml` file and make your updates:

| <pre><code>- DELIVERY_FOLDER=c:\aip-node-data\delivery
- DEPLOY_FOLDER=c:\aip-node-data\deploy
- SHARED_FOLDER=c:\aip-node-data\common-data
</code></pre> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- |

For example:

| <pre><code>- DELIVERY_FOLDER=\\shared\console\delivery
- DEPLOY_FOLDER=\\shared\console\deploy
- SHARED_FOLDER=\\shared\console\common-data
</code></pre> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- |

Save the file.

### Configure the Keycloak login credentials - optional

The Console authentication provider has been totally restructured in comparison to Console v. 1.x and now uses the open-source OAuth2 compatible [**Keycloak**](https://www.keycloak.org/) system. Keycloak provides local authentication, and can also interact with other enterprise authentication systems such as LDAP and SAML. Keycloak requires a **login** to administer it, and the default credentials are configured in the `docker-compose.yml` file in the `KEYCLOAK_USER` and `KEYCLOAK_PASSWORD` entries. By default the login credentials are set to **admin/admin**:

| <pre><code>  keycloak:
    image: castbuild/aip-sso
    container_name: keycloak
    ports:
      - 8086:8080
    restart: always
    environment:
      DB_VENDOR: POSTGRES
      DB_ADDR: postgres
      DB_USER: keycloak
      DB_PASSWORD: keycloak
      KEYCLOAK_USER: admin
      KEYCLOAK_PASSWORD: admin
      KEYCLOAK_IMPORT: /tmp/aip-realm.json
</code></pre> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

If you would like to change these credentials before deploying the Docker images, add the following lines to the `docker-compose.override.yml` file under the existing `services:` entry, update the values for the `KEYCLOAK_USER` and/or `KEYCLOAK_PASSWORD` entries and then save the file. For example to change the password to "cast":

| <pre><code>services:
...
	keycloak:
    	environment:
			KEYCLOAK_USER: admin
			KEYCLOAK_PASSWORD: cast
</code></pre> |
| ---------------------------------------------------------------------------------------------------------------------- |

## Step 4 - run the install

Run the following command from the unpacked ZIP folder:

| <pre><code>docker-compose up -d
</code></pre> |
| --------------------------------------------- |

\


| If you are facing a **permission denied** error when executing on Linux, you may need to elevate your user by running the **sudo command** specific to your Linux distribution (e.g. `sudo su` on Debian related distros). |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

This will start the install process and the containers will be downloaded from [**https://hub.docker.com/**](https://hub.docker.com/)**.** The **-d flag** causes the containers to be run in the background (see [**docker compose CLI reference**](https://docs.docker.com/compose/reference/) for more information).

![](https://doc.castsoftware.com/download/attachments/575766693/pull\_details.jpg?version=1\&modificationDate=1649342416033\&api=v2)

You should see the following when all containers have been pulled and started:

![](https://doc.castsoftware.com/download/attachments/575766693/pull\_complete.jpg?version=1\&modificationDate=1649342416020\&api=v2)

You can also check that the containers are up and running using the following command - see the "**status**" column:

| <pre><code>docker ps
</code></pre> |
| ---------------------------------- |

_Click to enlarge_

![](https://doc.castsoftware.com/download/attachments/575766693/docker\_ps.jpg?version=1\&modificationDate=1649342415847\&api=v2)

Your Console front-end is now up and running.

## What next?

Before proceeding with any further configuration, you should now **create at least one Node instance (AIP Core + Node service)**, see [**AIP Node service - back-end installation**](https://doc.castsoftware.com/display/AIPCONSOLEDRAFT/AIP+Node+service+-+back-end+installation).
