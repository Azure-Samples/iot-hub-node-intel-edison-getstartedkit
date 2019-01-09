

# Get Started with Microsoft Azure IoT Starter Kit - Intel Edison

**Note: this article has been archived and it is not longer updated. Instructions may not be totally up to date.**

This page contains technical information to help you get familiar with Azure IoT using the Azure IoT Starter Kit - Intel Edison. You will find two tutorials that will walk you through different scenarios. The first tutorial will show you how to connect your Azure IoT Starter kit to our Remote Monitoring preconfigured solution from Azure IoT Suite. In the second tutorial, you will leverage Azure IoT services to create your own IoT solution.

You can choose to start with whichever tutorial you want to. If you've never worked with Azure IoT services before, we encourage you to start with the Remote Monitoring solution tutorial, because all of the Azure services will be provisioned for you in a built-in preconfigured solution. Then you can explore how each of the services work by going through the second tutorial.

We hope you enjoy the tutorials! Please provide feedback if there's anything that we can improve.
 
***
**Don't have a kit yet?:** Click [here](http://azure.com/iotstarterkits)
***

- [Running a Simple Remote Monitoring Solution with the Intel Edison](#run-on-device)
- [Using Microsoft Azure IoT to Process and Use Sensor Data to Indicate Abnormal Temperatures](#using-microsoft-azure-iot)

<a name="run-on-device" />
# Running a Simple Remote Monitoring Solution with the Intel Edison

This tutorial describes the process of taking your Intel Edison Grove kit, and using it to develop a temperature, humidity and pressure reader that can communicate with the cloud using the  Microsoft Azure IoT SDK. 

## Table of Contents

- [1.1 Tutorial Overview](#section1.1)
- [1.2 Before Starting](#section1.2)
  - [1.2.1 Required Software](#section1.2.1)
  - [1.2.2 Required Hardware](#section1.2.2)
- [1.3 Create a New Azure IoT Suite Remote Monitoring solution and Add Device](#section1.3)
- [1.4 Prepare your device](#section1.4)
- [1.5 Connect the Grove Sensor Module to your Device](#section1.5)
- [1.6 Modify the Remote Monitoring sample](#section1.6)
- [1.7 View the Sensor Data from the Remote Monitoring Portal](#section1.7)
- [1.8 Next steps](#section1.8)

<a name="section1.1" />
## 1.1 Tutorial Overview

In this tutorial, you'll be doing the following:

- Setting up your environment on Azure using the Microsoft Azure IoT Suite Remote Monitoring preconfigured solution, getting a large portion of the set-up that would be required done in one step.
- Setting your device and sensors up so that it can communicate with both your computer, and Azure IoT. 
- Updating the device code sample to include our connection data and send it to Azure to be viewed remotely.

<a name="section1.2" />
## 1.2 Before Starting

<a name="section1.2.1" />
### 1.2.1 Required Software

- Intel Edison drivers, available here: 
  - [Windows users](https://software.intel.com/en-us/get-started-edison-windows)
  - [Mac users](https://software.intel.com/en-us/get-started-edison-osx)
  - [Linux users](https://software.intel.com/en-us/get-started-edison-linux)
   
<a name="section1.2.2" />
### 1.2.2 Required Hardware

- Intel Edison Grove kit
  - Two USB Mini cables 
  
<a name="section1.3" />
## 1.3 Create a New Azure IoT Suite Remote Monitoring solution and Add Device

- Log in to [Azure IoT Suite](https://www.azureiotsuite.com/)  with your Microsoft account and click **Create a New Preconfigured Solution** 

***
**Note:** For first time users, click here to get your [Azure free trial](https://azure.microsoft.com/en-us/pricing/free-trial/) which gives you 200USD of credit to get started.
***

- Click select in the **Remote Monitoring** option
- Type in a solution name. For this tutorial we’ll be using _“EdisonSuite”_ (You will have to name it something else. Make it unique. I.E. "ContosoSample")

***
**Note:** Make sure to copy down the names and connection strings mentioned into a text document for reference later.
***

- Choose your subscription plan and geographic region, then click **Create Solution**
- Wait for Azure to finish provisioning your IoT suite (this process may take up to 10 minutes), and then click **Launch**

***
**Note:** You may be asked to log back in. This is to ensure your solution has proper permissions associated with your account. 
***

- Open the link to your IoT Suite’s “Solution Dashboard.” You may have been redirected there already. 
- This opens your personal remote monitoring site at the URL _&lt;Your Azure IoT Hub suite name&gt;.azurewebsites.net_ (e.g. _EdisonSuite.azurewebsites.net_)
- Click **Add a Device** at the lower left hand corner of your screen
- Add a new **custom device**
- Enter your desired `device ID`. In this case we’ll use _“Edison_Grove”_, and then click Check ID
- If Device ID is available, go ahead and click **Create**
- Make note of your `device ID`, `Device Key`, and `IoT Hub Hostname` to enter into the code you’ll run on your device later 

***
**Warning:** The Remote Monitoring solution provisions a set of Azure IoT Services in your Azure account. It is meant to reflect a real enterprise architecture and thus its Azure consumption is quite heavy. To avoid unnecessary Azure consumption, we recommend you delete the preconfigured solution in azureiotsuite.com once you are done with your work (since it is easy to recreate). Alternatively, if you want to keep it up and running you can do several things to reduce consumption:

1) Visit this [guide](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md) to run the solution in demo mode and reduce the Azure consumption.  

2) Disable the simulated devices created with the solution (Go to Devices>>Select the device>> on the device details menu on the right, clich on Disable Device. Repeat with all the simulated devices).

3) **Stop** your remote monitoring solution while you are working on the next steps. (See: [Troubleshooting](#troubleshooting))
***

- For additional references, refer to the following:
 - https://azure.microsoft.com/en-us/documentation/articles/iot-suite-remote-monitoring-sample-walkthrough/
 - https://azure.microsoft.com/en-us/documentation/articles/iot-suite-connecting-devices/
 
 <a name="section1.4" />
## 1.4 Prepare your device

If this is the first time you use your Intel Edison board, you will have to follow some steps to assemble it.

- Please follow steps 1-4 of the [instructions](https://software.intel.com/en-us/iot/library/edison-getting-started). Intel's IDE (Step 5) is optional for our tutorial.
- Flash your board with the latest [firmware](https://www-ssl.intel.com/content/www/us/en/support/boards-and-kits/000005990.html)

<a name="section1.5" />
## 1.5 Connect the Grove Sensor Module to your Device

- Begin by placing the Base Shield over the pins on your Intel Edison board
- Using [this image](https://github.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/blob/master/img/edison_remote_monitoring.png?raw=true) as a reference, connect your Grove sensor and the Intel Edison
- For sensor pins, we will use the following wiring:

| Edison Port   | Component    | 
| ------------- | ------------ | 
| A0            | Temperature  | 

- For more information, see: [Seeed studio's wiki](http://www.seeedstudio.com/wiki/Base_shield_v2).

**At the end of your work, your Intel Edison should be connected with a working sensor. We'll test it in the next sections.**

<a name="section1.6" />
## 1.6 Modify, build and run the Remote Monitoring sample 

- In your Edison boards command line, type the following command to transfer the files to your board:

```  
wget https://raw.githubusercontent.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/master/remote_monitoring/remote_monitoring.js
wget https://raw.githubusercontent.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/master/remote_monitoring/package.json
```  

- Open the file **remote_monitoring.js** in a text editor using the command:

```
nano remote_monitoring.js
```

- Nano is generally not installed with Yocto build. If you don't have it installed already, here are the instructions to install nano on your intel edision:

```
mkdir nano-installer
cd nano-installer
wget http://www.nano-editor.org/dist/v2.2/nano-2.2.6.tar.gz 
tar xvf nano-2.2.6.tar.gz 
cd nano-2.2.6 
./configure && make && make install

```

- Locate the following code in the file and update your connection data:

```
var hostName = '<IOTHUB_HOST_NAME>';
var deviceId = '<DEVICE_ID>';
var sharedAccessKey = '<SHARED_ACCESS_KEY>';
```

- Save your changes by pressing Ctrl+O and when nano prompts you to save it as the same file, just press ENTER.

- Press Ctrl+X to exit nano.

- Run the sample application using the following commands:

```
npm install
node remote_monitoring.js
```

<a name="section1.7" />
## 1.7 View the Sensor Data from the Remote Monitoring Portal

- Once you have the sample running, visit your dashboard by visiting azureiotsuite.com and clicking “Launch” on your solution
- Make sure the “Device to View” in the upper right is set to your device
- If the demo is running, ou should see an output on your command window that shows your device details and the sensor data. Also, check out how the graph changes in real time as your data updates!

***
**Note:** Make sure you delete or  **stop** your remote monitoring solution once you have completed this to avoid unnecesary Azure consumption! Check out the troubleshooting section for more details.
***

<a name="section1.8" />
## 1.8 Next steps

Please visit our [Azure IoT Dev Center](https://azure.microsoft.com/en-us/develop/iot/) for more samples and documentation on Azure IoT.

<a name="using-microsoft-azure-iot" />
# Using Microsoft Azure IoT Services to Identify Temperature Anomalies

This tutorial describes the process of taking your Microsoft Azure IoT Starter Kit for the Intel Edison, and using it to develop a temperature and humidity reader that can communicate with the cloud using the  Microsoft Azure IoT SDK.

## Table of Contents

- [2.1 Tutorial Overview](#section2.1)
- [2.2 Before Starting](#section2.2)
  - [2.2.1 Required Software](#section2.2.1)
  - [2.2.2 Required Hardware](#section2.2.2)
- [2.3 Connect the Sensor Module to your Device](#section2.3)
- [2.4 Create a New Microsoft Azure IoT Hub and Add Device](#section2.4)
- [2.5 Create an Event Hub](#section2.5)
- [2.6 Create a Storage Account for Table Storage](#section2.6)
- [2.7 Create a Stream Analytics job to Save IoT Data in Table Storage and Raise Alerts](#section2.7)
- [2.8 Node Application Setup](#section2.8)
- [2.9 Modify Device Sample](#section2.9)
- [2.10 View Your Command Center Application](#section2.10)
- [2.11 Next steps](#section2.11)

<a name="section2.1" />
## 2.1 Tutorial Overview

This tutorial has the following steps:

- Provision an IoT Hub instance on Microsoft Azure and adding your device. 
- Prepare the device, get connected to the device, and set it up so that it can read sensor data.
- Configure your Microsoft Azure IoT services by adding Event Hub, Storage Account, and Stream Analytics resources.
- Prepare your local web solution for monitoring and sending commands to your device.
- Update the sample code to respond to commands and include the data from our sensors, sending it to Microsoft Azure to be viewed remotely.

The end result will be a functional command center where you can view the history of your device's sensor data, a history of alerts, and send commands back to the device.

<a name="section2.2" />
## 2.2 Before Starting

<a name="section2.2.1" />
### 2.2.1 Required Software

- [Git](https://git-scm.com/downloads) - For cloning the required repositories
- Node.js - For the Node application, we will go over this later.
- Intel Edison drivers, available here: 
  - [Windows users](https://software.intel.com/en-us/get-started-edison-windows)
  - [Mac users](https://software.intel.com/en-us/get-started-edison-osx)
  - [Linux users](https://software.intel.com/en-us/get-started-edison-linux)
   
<a name="section2.2.2" />
### 2.2.2 Required Hardware

- Intel Edison Grove kit
  - Two USB Mini cables 

<a name="section2.3" />
## 2.3 Connect the Sensor Module to your Device

- Begin by placing the Base Shield over the pins on your Intel Edison board
- Using [this image](https://github.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/blob/master/img/edison_command_center.png?raw=true) as a reference, connect your Grove sensor and the Intel Edison
- For sensor pins, we will use the following wiring:

| Edison Port   | Component       | 
| ------------- | --------------- | 
| A0            | Temperature     | 
| D8            | LED Socket Kit  | 

- For more information, see: [Seeed studio's wiki](http://www.seeedstudio.com/wiki/Base_shield_v2).

**At the end of your work, your Intel Edison should be connected with a working sensor. We'll test it in the next sections.**

<a name="section2.4" />
## 2.4 Create a New Microsoft Azure IoT Hub and Add Device

- To create your Microsoft Azure IoT Hub and add a device, follow the instructions outlined in the [Setup IoT Hub Microsoft Azure Iot SDK page](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).
- After creating your device, make note of your connection string to enter into the code you’ll run on your device later

***
**Note:** Make sure to copy down the names and connection strings mentioned into a text document for reference later.
***

<a name="section2.5" />
## 2.5 Create an Event Hub
Event Hub is an Azure IoT publish-subscribe service that can ingest millions of events per second and stream them into multiple applications, services or devices.
- Log on to the [Microsoft Azure Portal](https://portal.azure.com/)
- Click on **New** -&gt; **Internet of Things**-&gt; **Event Hub**
- After being redirected, click "Custom Create", Enter the following settings for the Event Hub (use a name of your choice for the event hub and the namespace):
    - Event Hub Name: `EdisonEventHub`
    - Region: `Your choice`
    - Subscription: `Your choice`
    - Namespace Name: `Your Project Namespace, in our case “Edison2Suite”`
- Click the **arrow** to continue.
- Choose to create **4** partitions and retain messages for **7** days.
- Click the **check** at the bottom right hand corner to create your event hub.
- Click on your `Edison2Suite` service bus (what you named your service bus)
- Click on the **Event Hubs** tab
- Select the `EdisonEventHub` eventhub and go in the **Configure** tab in the **Shared Access Policies** section, add a new policy:
    - Name = `readwrite`
    - Permissions = `Send, Listen`
- Click **Save** at the bottom of the page, then click the **Dashboard** tab near the top and click on **Connection Information** at the bottom
- _Copy down the connection string for the `readwrite` policy you created._

<a name="section2.6" />
## 2.6 Create a Storage Account for Table Storage
Now we will create a service to store our data in the cloud.
- Log on to the [Microsoft Azure Portal](https://portal.azure.com/)
- In the menu, click **New** and select **Data + Storage** then **Storage Account**
- Choose **Classic** for the deployment model and click on **Create**
- Enter the name of your choice (We chose `edisonstorage`) for the account name, `Standard-RAGRS` for the type, choose your subscription, select the resource group you created earlier, then click on **Create**
- You should try to create the storage account in the same region as your IoT Hub and Event Hub if possible.  
- Once the account is created, find it in the **resources blade** or click on the **pinned tile**, go to **Settings**, **Keys**, and write down the _primary connection string_.

<a name="section2.7" />
## 2.7 Create a Stream Analytics job to Save IoT Data in Table Storage and Raise Alerts
Stream Analytics is an Azure IoT service that streams and analyzes data in the cloud. We'll use it to process data coming from your device.
- Log on to the [Microsoft Azure Portal](https://portal.azure.com/)
- In the menu, click **New**, then click **Internet of Things**, and then click **Stream Analytics Job**
- Enter a name for the job (We chose “EdisonStorageJob”), a preferred region, then choose your subscription. At this stage you are also offered to create a new or to use an existing resource group. Choose the resource group you created earlier (In our case, `Edison2Suite`).
- Once the job is created, open your **Job’s blade** or click on the **pinned tile**, and find the section titled _“Job Topology”_ and click the **Inputs** tile. In the Inputs blade, click on **Add**
- Enter the following settings:
    - Input Alias = _`TempSensors`_
    - Source Type = _`Data Stream`_
    - Source = _`IoT Hub`_
    - IoT Hub = _`Edison2Suite`_ (use the name for the IoT Hub you create before)
    - Shared Access Policy Name = _`service`_
    - Shared Access Policy Key = _The `service` primary key saved from earlier_
    - IoT Hub Consumer Group = "" (leave it to the default empty value)
    - Event serialization format = _`JSON`_
    - Encoding = _`UTF-8`_

- Back to the **Stream Analytics Job blade**, click on the **Query tile** (next to the Inputs tile). In the Query settings blade, type in the below query and click **Save**:

```
SELECT
    DeviceId,
    EventTime,
    MTemperature as TemperatureReading
INTO
    TemperatureTableStorage
from TempSensors
WHERE
   DeviceId is not null
   and EventTime is not null

SELECT
    DeviceId,
    EventTime,
    MTemperature as TemperatureReading
INTO   
    TemperatureAlertToEventHub
FROM
    TempSensors
WHERE MTemperature > 25 
```

***
**Note:** You can change the `25` to `0` when you're ready to generate alerts to look at. This number represents the temperature in degrees celsius to check for when creating alerts. 25 degrees celsius is 77 degrees fahrenheit.
***

- Back to the **Stream Analytics Job blade**, click on the **Outputs** tile and in the **Outputs blade**, click on **Add**
- Enter the following settings then click on **Create**:
    - Output Alias = _`TemperatureTableStorage`_
    - Sink = _`Table Storage`_
    - Storage account = _`edisonstorage`_ (The storage you made earlier)
    - Storage account key = _(The primary key for the storage account made earlier, can be found in Settings -&gt; Keys -&gt; Primary Access Key)_
    - Table Name = _`TemperatureRecords`_ Your choice - If the table doesn’t already exist, Local Storage will create it
    - Partition Key = _`DeviceId`_
    - Row Key = _`EventTime`_
    - Batch size = _`1`_
- Back to the **Stream Analytics Job blade**, click on the **Outputs tile**, and in the **Outputs blade**, click on **Add**
- Enter the following settings then click on **Create**:
    - Output Alias = _`TemperatureAlertToEventHub`_
    - Source = _`Event Hub`_
    - Service Bus Namespace = _`Edison2Suite`_
    - Event Hub Name = _`edisoneventhub`_ (The Event Hub you made earlier)
    - Event Hub Policy Name = _`readwrite`_
    - Event Hub Policy Key = _Primary Key for readwrite Policy name (That's the one you wrote down after creating the event hub)_
    - Partition Key Column = _`0`_
    - Event Serialization format = _`JSON`_
    - Encoding = _`UTF-8`_
    - Format = _`Line separated`_
- Back in the** Stream Analytics blade**, start the job by clicking on the **Start **button at the top

***
**Note:** Make sure to **stop** your Command Center jobs once you have when you take a break or finish to avoid unnecessary Azure consumption!  (See: [Troubleshooting](#troubleshooting))
***

<a name="section2.8" />
## 2.8 Node Application Setup

 - If you do not have it already, install Node.js and NPM.
   - Windows and Mac installers can be found here: https://nodejs.org/en/download/
     - Ensure that you select the options to install NPM and add to your PATH.
   - Linux users can use the commands:

```
sudo apt-get update
sudo apt-get install nodejs
sudo apt-get install npm
``` 

- Additionally, make sure you have cloned the project repository locally by issuing the following command in your desired directory:

```
git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit.git
```

- Open the `command_center_node` folder in your command prompt (`cd path/to/command_center_node`) and install the required modules by running the following:

```
npm install -g bower
npm install
bower install
```

- Open the `config.json` file and replace the information with your project.  See the following for instructions on how to retrieve those values.

    - eventhubName: 
        - Open the [Classic Azure Management Portal](https://manage.windowsazure.com)
        - Open the Service Bus namespace you created earlier
        - Switch to the **EVENT HUBS** page 
        - You can see and copy the name of your event hub from that page
    - ehConnString: 
        - Click on the name of the event hub from above to open it
        - Click on the "CONNECTION INFORMATION" button along the bottom. 
        - From there, click the button to copy the readwrite shared access policy connection string.
    - deviceId:
        - Use the information on the [Manage IoT Hub](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md) to retrieve your deviceId using either the Device Explorer or iothub-explorer tools.
    - iotHubConnString: 
        - In the [Azure Portal](https://portal.azure.com)
        - Open the IoT Hub you created previously. 
        - Open the "Settings" blade
        - Click on the "Shared access policies" setting
        - Click on the "service" policy
        - Copy the primary connection string for the policy
    - storageAccountName:
        - In the [Azure Portal](https://portal.azure.com)
        - Open the classic Storage Account you created previously to copy its name
    - storageAccountKey:
        - Click on the name of the storage account above to open it
        - Click the "Settings" button to open the Settings blade
        - Click on the "Keys" setting
        - Click the button next to the "PRIMARY ACCESS KEY" top copy it
    - storageTableName:
        - This must match the name of the table that was used in the Stream Analytics table storage output above.
        - If you used the instructions above, you would have named it ***`TemperatureRecords`*** 
        - If you named it something else, enter the name you used instead.    
        
```
{
    "port": "3000",
    "eventHubName": "event-hub-name",
    "ehConnString": "Endpoint=sb://name.servicebus.windows.net/;SharedAccessKeyName=readwrite;SharedAccessKey=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa=",
    "deviceId": "iot-hub-device-name",
    "iotHubConnString": "HostName=iot-hub-name.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey=aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa=",
    "storageAcountName": "aaaaaaaaaaa",
    "storageAccountKey": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa==",
    "storageTable": "TemperatureRecords"
} 
```

- Now it is time to run it! Enter the following command:

```
node server.js
```

- You should then see something similar to:
```
app running on http://localhost:3000
``` 

- Visit the url in your browser and you will see the Node app running!
 
To deploy this project to the cloud using Azure, you can reference [Creating a Node.js web app in Azure App Service](https://azure.microsoft.com/en-us/documentation/articles/web-sites-nodejs-develop-deploy-mac/).

Next, we will update your device so that it can interact with all the things you just created.

<a name="section2.9" />
## 2.9 Modify Device Sample

- In your Edison boards command line, type the following command to transfer the files to your board:

```  
wget https://raw.githubusercontent.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/master/command_center/command_center.js
wget https://raw.githubusercontent.com/Azure-Samples/iot-hub-node-intel-edison-getstartedkit/master/command_center/package.json
```  
 
- Open the file **command_center.js** in a text editor using the command:

```
nano command_center.js
```

- Nano is generally not installed with Yocto build. If you don't have it installed already, here are the instructions to install nano on your intel edision:

```
mkdir nano-installer
cd nano-installer
wget http://www.nano-editor.org/dist/v2.2/nano-2.2.6.tar.gz 
tar xvf nano-2.2.6.tar.gz 
cd nano-2.2.6 
./configure && make && make install

```

- Locate the following code in the file and update your connection data.  See below for information how how to retrieve these values:

    - connectionString:
        - Use the **"Device Explorer"** or **"iothub-explorer"** tools as documented on the [Manage IoT Hub](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md) page to retrieve the connection string for the device you created previously.
        - Replace `<IOT_HUB_DEVICE_CONNECTION_STRING>` below with that name.
    - sharedAccess

```
var connectionString = '<IOT_HUB_DEVICE_CONNECTION_STRING>';
```
- Save your changes by pressing Ctrl+O and when nano prompts you to save it as the same file, just press ENTER.

- Press Ctrl+X to exit nano.

- Run the sample application using the following commands:

```
npm install
node command_center.js
```
- Data is now being sent off at regular intervals to Microsoft Azure!

<a name="section2.10" />
## 2.10 View Your Command Center Application

Head back to your Node application and you will have a fully functional command center, complete with a history of sensor data, alerts that display when the temperature got outside a certain range, and commands that you can send to your device remotely.

***
**Note:** Make sure to **stop** your Command Center jobs once you have when you finish to avoid unnecessary Azure consumption!  (See: [Troubleshooting](#troubleshooting))
***

<a name="section2.11" />
## 2.11 Next steps

Please visit our [Azure IoT Dev Center](https://azure.microsoft.com/en-us/develop/iot/) for more samples and documentation on Azure IoT.


# Troubleshooting

## Stopping Provisioned Services

- In the [Microsoft Azure Portal](https://portal.azure.com/)
    - Click on "All Resources"
    - For each Stream Analytics and Web App resource:
        - Click on the resource and click the "Stop" button in the new blade that appears
    - For each IoT Hub resource:
        - Click on the resource and click the "Devices" button in the new blade that appears
        - Click on each device in the list and click the "Disable" button that appears in the new blade at the bottom
