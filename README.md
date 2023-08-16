# Failed RDP Logins Source IP to full GeoData Conversion
<h2>Description</h2>
In this lab, we will create a honeypot in Azure and map each log in attempt to the location on the world map. We achieve this using a powershell shell script originally written by <a href= "https://github.com/joshmadakor1" > Joshmadakor</a>. A honeypot is a device intentionally exposed to the internet to entice attackers to attempt to log into the machine. This is usually done for research purposes - as in this case - and leaving a device is not advisable security practice.

<br />

<h2>Languages</h2>

- <b>PowerShell</b> 
- <b>KQL</b>
- <b>html</b>

<h2>Requirements</h2>

- <b>Azure Account</b>
- <b>IP geolocation API key https://ipgeolocation.io/</b>

<h2>Environments Used </h2>

- <b>Windows 11</b> (21H2)

<h2>Project walk-through:</h2>


The steps to recreate this project are below; <br/>
Step 1: Create azure account <br>
Step 2: Create resource group<br>
Step 3: Create vm <br>
Step 4: Create Log Analytics Workspace (LAW) <br>
Step 5: Set up data collection <br>
Step 6: Connect data collectors to virtual machine<br>
Step 7: Set up vm<br>
Step 8 : Create custom log in LAW and filter based on parameters<br>
    -Create map of logs<br>

<p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><strong>Step 1</strong> - free account</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">You can sign up for a free azure account by visiting <a href="https://azure.microsoft.com/en-gb/free">https://azure.microsoft.com/en-gb/free</a></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><strong>Step 2</strong> - Create a resource group</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">After your account has been set up, click on the search bar and search &quot;resource groups&quot;, create a new resource group to be used during this project.</p><br>
<p></p>
<br/>
<img src="https://i.imgur.com/PpDCMJj.png" height="80%" width="80%" />
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Creating resource group</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Step 3 - create VM</p><br>
<br/>
<img src="https://i.imgur.com/s9YpkCm.png" height="80%" width="80%" />
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Note: during initial testing, we had a DOS attack that knocked the server offline as we tried to use the minimum requirements. As a result we advise you use a minimum of 8GiB RAM to give your system a better response and slightly better resilience.</p>
<p><br></p>
<img src="https://i.imgur.com/xRUqOYJ.png" height="80%" width="80%" /><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Under the &quot;Networking&quot; tab when creating your VM, go to &quot;NIC network security group&quot; and select Advanced then create a network security group (<span style="font-weight:bold">NSG</span> -<span style="font-style:italic">The NSG is essentially a firewall and the rules on traffic flow is configured here.</span>)</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><br></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Delete any existing rules and create an inbound rule to match the below and change the rule priority to 100;</p><br>
<img src="https://i.imgur.com/BW1PC1y.png" height="60%" width="40%" /><br>
<img src="https://i.imgur.com/i3utrUi.png" height="40%" width="40%" /><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">This will allow any inbound traffic over any port thus making the machine a interesting target to attackers.</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><strong>Step 4; Create Log Analytics workspace</strong></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Search &quot;Log analytics Workspace&quot; &gt; select the option from the drop down &gt; create</p>
<img src="https://i.imgur.com/oec9y0R.png" height="60%" width="40%" /><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><span style="font-size: 11pt; text-align: inherit;"><strong>Step 5; set up data collection</strong></span><strong><br></strong></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Search &quot;Microsoft Defender for cloud&quot; &gt; Environment settings. Open the drop down and select your LAW for this project</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><br></p>
<img src="https://i.imgur.com/Ccc58UF.png" height="80%" width="80%" /><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">After Selecting the workspace, Check that SQL server is off and &quot;servers&quot; is on
<img src="https://i.imgur.com/gkuFe3G.png" height="80%" width="80%" /><br></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Then set &quot;data collection&quot; to all events and save:&nbsp;</p>
<img src="https://i.imgur.com/R3Xi7im.png" height="80%" width="80%" /><br></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><strong>Step 6: Connect data collectors to virtual machine</strong></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Search LAW, then look for &quot;virtual machine&quot; in the left column:
</p>
<img src="https://i.imgur.com/iyGZMzj.png" height="80%" width="80%" /><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">select the vm and connect the virtual machine&nbsp; to the LAW:</p>
<img src="https://i.imgur.com/oXqDCr2.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><span 
style="text-align: inherit;"><strong>Step 7: Set up vm</strong></span></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><span style="font-size: 11pt; text-align: inherit;">Using RDP from your local desktop, connect to your VM it&apos;s public Ip address:</span></p>
<img src="https://i.imgur.com/QCf1Q9i.png" height="60%" width="60%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Turn off the firewall on the vm. This has to be done under each profile tab. i.e. domain, private, public</p>
<img src="https://i.imgur.com/DqtkoXc.png" height="60%" width="60%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Using PowerShell ISE; Copy, paste and run the script&nbsp;</p>
<img src="https://i.imgur.com/G8qhd70.png" height="80%" width="80%" /><p
style="margin:0in;font-family:Calibri;font-size:11.0pt"><span style="font-size: 11pt; text-align: inherit;">Output of the Ps1. script</p><br>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><strong>Step 8: Create custom log in LAW and filter based on parameters</strong></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Back in the azure portal, open LAW &gt; Tables &gt; Create custom log&nbsp;</p>
<img src="https://i.imgur.com/EM8jHp8.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Copy the log file created on the vm and paste it on your local desktop so it can be used to train the log analytics workspace. c:\programdata\(*filename*)</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><br></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Import failed_rdp logs into LAW</p>
<img src="https://i.imgur.com/hLNeMXs.png" height="40%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">under &quot;collection path&quot;, specify the path to the file with the logs on the vm</p>
<img src="https://i.imgur.com/ESjBwb5.png" height="40%" width="80%" /><br><p
style="margin:0in;font-family:Calibri;font-size:11.0pt">save the custom log.</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt;">In the column on the left, navigate to &quot;Logs&quot; and search for the Custom Log name. It might take up to 30 minutes for the log to appear here:</p>
<img src="https://i.imgur.com/XlNfBqV.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Filter the data in the &quot;raw data&quot; field by specified parameters:</p>

```
failed_rdp_with_geo_CL
| parse RawData with * "latitude=" latitude " longitude=" longitude " destinationhost=" destinationhost " sourcehost=" sourcehost " country=" country " label=" label
| project latitude, longitude, sourcehost, country, label, destinationhost

```




<img src="https://i.imgur.com/bHNd8Ii.png" height="80%" width="80%" /><br><p></p>

<p style="margin:0in;font-family:Calibri;font-size:11.0pt"><span style="font-size: 11pt; text-align: inherit;"><strong>Step 9: Create workbook for incident and create map visualization</strong></span><strong><br></strong></p><strong>
</strong>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">&nbsp;From the column on the left, select workbook &gt; New &nbsp;</p>
<img src="https://i.imgur.com/vZDG6Ti.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Remove all existing queries by clicking on the 3 dots &gt; Remove</p>
<img src="https://i.imgur.com/aAEu0ng.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Add a new query to the workbook</p>
<img src="https://i.imgur.com/zn3Zvni.png" height="80%" width="80%" /><br><p></p>

```
failed_rdp_with_geo_CL
| parse RawData with * "latitude=" latitude " longitude=" longitude " destinationhost=" destinationhost " sourcehost=" sourcehost " country=" country " label=" label
| project latitude, longitude, sourcehost, country, label, destinationhost
| summarize event_count=count() by sourcehost, label, country, longitude, latitude|<br> where sourcehost != "" 
```

<p style="margin:0in;font-family:Consolas;font-size:10.5pt">Copy and paste the code above as the query. This filters the logs but also keeps count of how many events originated from a location. </p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">
</p>
<p style="margin:0in;font-family:Consolas;font-size:10.5pt">Under &quot;visualization&quot; select &quot;map&quot; and &quot;full&quot; under &quot;size&quot;</p>
<img src="https://i.imgur.com/Lrkfp9c.png" height="50%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Failed login attempts mapped to country:</p>
<img src="https://i.imgur.com/fR39UHZ.png" height="80%" width="80%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Our map was created using the country or region location info but this can also be done with the longitude and latitude information gathered. Our exact map settings are below</p>
<img src="https://i.imgur.com/j7mpfWX.png" height="80%" width="40%" /><br><p></p>
<img src="https://i.imgur.com/DCIn2to.png" height="80%" width="40%" /><br><p></p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">Save the map setting and the edited query. </p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">
</p>
<p style="margin:0in;font-family:Calibri;font-size:11.0pt">After the workbook has been saved, it can be set to auto refresh at intervals:</p>
<img src="https://i.imgur.com/eSzXaH4.png" height="80%" width="80%" /><br><p></p>

