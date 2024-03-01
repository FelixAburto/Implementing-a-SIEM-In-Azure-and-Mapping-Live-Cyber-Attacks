<h1>Implementing a SIEM in Microsoft Azure and Mapping Live Cyber Attacks</h1>


<h2>Description</h2>
<b>
</b
<br />
<br />
This project will consist of creating a vulnerable virtual machine that will act as our honeypot then creating a Log Aggregation Workspace to transfer our logs from the virtual machine. Next we will set up Microsoft Sentinel and connect our virtual machine logs to it via the Log Aggregation Workspace. Finally we will create map that illustrates failed RDP login attempts from all over the world. 
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/e1T4Zjs.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>
</p>
<h2>Languages Used</h2>

- <b>PowerShell:</b> Used to extract and transform RDP failed logon logs from Windows Event Viewer 

<h2>Utilities Used</h2>

- <b>ipgeolocation.io:</b> IP Address to Geolocation API
- <b>Microsoft Azure:</b> Virtual Machine, SIEM, and Logging


<h2>Project Walkthrough</h2>

### Creating the Honey Pot:

<br />
The first thing to do is create a honey pot virtual machine in Azure and configure its firewall to allow all traffic into the virtual machine:

<br />

<br />
<br />

<p align="center">
<img src="https://i.imgur.com/a4fozi4.png" height="50%" width="50%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/nNEhiEa.png" height="50%" width="50%" alt="Image Analysis Dataflow"/>
</p>


<br />
Once the VM is created we are going to log into the machine using Remote Desktop Connection. Once we are logged in we going to turn off the firewall by typing "wf.msc" into the search bar, click on "Windows Defender Firewall Properties" and set the firewall state to "Off" in the Domain Profile, Private Profile, and Public Profile". After that we can verify that we are able to communicate to the VM from the internet by pinging it from your host machine:

<br />

<br />
<br />

<p align="center">
<img src="https://i.imgur.com/Kg8pESS.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/xtPAoxN.png" height="50%" width="50%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/iyTyVda.png" height="50%" width="50%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/jusU4yH.png" height="50%" width="50%" alt="Image Analysis Dataflow"/>
</p>

<br />

### Creating the Log Analytics Workspace:


Next we will create a Log Analytics Workspace for the honeypot. The purpose of the is to ingest the Windows Security Event logs into the LAW so we can transfer the logs into the SIEM once we have it setup:

<br />

<p align="center">
<img src="https://i.imgur.com/IBe37VT.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/4AHSoz4.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />

### Configuring Microsoft Defender Cloud:


The next step is to enable the ability to gather logs from the honey pot. To do this we need to go to Microsoft Defender Cloud. Once there click on "Environment Settings" and click on "Expand All". We should see the LAW that we created for our honey pot. Click on the LAW we created and enable "Foundational CSPM" and "Severs". Next click on "Data Collection" on the left side of the screen and enable "All Events":  

<br />


<p align="center">
<img src="https://i.imgur.com/McjCvCX.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/fXQ3bqz.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/Sc6MtMf.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>


<br />

### Connecting LAW to the Honey Pot:


After this we go back to the Log Analytics Workspace that we created and we are going to connect our honey pot to it. To do this click on "Virutual Machines" on the left side of the screen. Click on the VM that we made and click "Connect":  

<br />

<p align="center">
<img src="https://i.imgur.com/00s3pY7.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/cYaxzDP.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/uqtP1iH.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>


<br />

### Setting up Microsfot Sentinal:


Next we are going to set up Microsoft Sentinal and connect the Log Aggregation Workspace. To do this we go to Microsoft Sentinal in Azure and click on "Create Microsoft Sentinel". Next we will just select your LAW that you created and it will start the process of connecting them to each other:  

<br />

<p align="center">
<img src="https://i.imgur.com/IqetdbL.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>

<br />

### Running PowerShell Script for Custom Log Creation:

The next step will be to log into our honeypot VM with Remote Desktop Connection. Once we are logged in run the PowerShell script "Custom_Security_Log_Exporter.ps1". The script is designed to parse out the data within the Windows Event Logs for failed RDP login attacks. The script also uses a third party API to capture geographic information about the attackers location.

(***Credit for the PowerShell Script goes to Josh Madakor or "joshmadakor1" on GitHub***)
<br />
</p>

<p align="center">
<img src="https://i.imgur.com/qcnFqr0.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>

<br />

### Creating Custom Logs:

Next we will create a custom log within our Log Analytics Workspace. This will allows us to bring over the failed RDP log file that the script created into the LAW which will allow us to connected it into the Microsoft Sentinel. To create our custom logs we'll need to go to "Tables" under the LAW that we created. Click on create and select "New custom log (MMA-based)". Next it will ask us to input a sample log file and we will use the log file that the PowerShell script has created inside "C:\ProgramData\". We can do this by copying the text from the failed_rdp.log file and pasting it to a txt file on our host computer. From there simply select the txt file that we created and click next until we are at the "Collection Path" screen. Here we will input the "C:\ProgramData\failed_rdp.log" file path which is where the log exists within the VM. Next we are going to enter in the custom log name (this can be whatever we want) and finally we're going review and create our custom logs.
<br />

<p align="center">
<img src="https://i.imgur.com/HEFiWWB.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/O654beR.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/79AZmT9.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/okwhWub.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/3EeT8Gx.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />
Afterwards we need to make sure our custom log is working properly. To do this we are going to select "Logs" within our LAW. This should open up a section where we can input the name of our custom log and run it. If the custom log is running properly it should look like this:
<br />

<br />

***(Note: When first creating the custom log it may take up to 20-30 minutes for the custom log to sync up Azure. As long as you dont see an error message while trying to run the log it should be fine.)***

<br />

<p align="center">
<img src="https://i.imgur.com/qdfvgq7.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />

### Creating a Workbook in Microsoft Sentinel:


The next step will be to create a workbook within the Microsoft Sentinel that we set up earlier. To do this we are going to go to our Sentinal that we created and on the next page click on "Workbook" under Threat Management. Afterwards click on "Add Workbook" and on the next page delete the default items by clicking "Edit". We should see three dots appear next to the items. Click on the the three dots and click remove for each of them until there is nothing left. After the items are gone we should see the option to add new items. Click on that and select "Add Query" from the drop down menu. 
<br />

<br />
<br />

<p align="center">
<img src="https://i.imgur.com/tmnynBD.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/3DqafwY.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<p align="center">
<img src="https://i.imgur.com/ATCkmv1.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />

### Using KQL to Extract Information:

Next we will add KQL Regex Query to extract relevant information needed to map out the cyber attacks within Microsoft Sentinel. The query is included in a txt file within the repository. After we enter in the query run it to make sure it is extracting the data correctly. It should look like this afterwards:
<br />

<br />
<br />

<p align="center">
<img src="https://i.imgur.com/QQQ7IUL.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />

### Creating The Map of Live Cyber Attacks:


After the query is done executing click on the drop down menu for "Visualization" and click on the option for "Map". After we've done this click on "Map Setting" and go to the panel located to the right of the screen. Under "Metric Settings" click on the drop down menu for "Metric Label" and select "label" and click apply. This should organize the data to display the country as well as the IP address associated with it. Once this is done click on "Done editing on the top right to save the map in your workbook:
<br />

<br />
<br />

<p align="center">
<img src="https://i.imgur.com/JF3IwQo.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />
Finally, we will have finished creating a map of failed RDP login attempts to your honeypot within Microsoft Sentinel. As you can see it displays login attempts from all over the world and there are thousands upon thousands of attempts done. This illustrates that it is very important to secure systems that are exposed to the internet because there is always going to be someone trying to gain access to your system.
<br />




<h2>World map of incoming attacks after 24 hours (built custom logs including geodata)</h2>

<p align="center">
<img src="https://i.imgur.com/At8DEPy.png" height="100%" width="100%" alt="Image Analysis Dataflow"/>
</p>


