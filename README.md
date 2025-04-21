# Emulated-SOC-SIEM-honeypot

## Objective

The Emulated SOC+SIEM honeypot project aimed to establish a controlled environment for simulating and detecting cyber attacks. The primary focus was to analyze real-world attack data, using Microsoft Azure to spin up an exposed Virtual Machine(vm) onto the open Internet, to then ingest and analyze its logs within the Microsoft Sentinel Security Information and Event Management (SIEM) system, and finally interpret the incoming logs to build a real-time attack map. This hands-on experience was designed to deepen my understanding of Microsoft Sentinel, log forwarding and integration, and real-time data visualization.

### Skills Learned

- Advanced understanding of Microsoft Azure objects.
- Proficiency in analyzing and interpreting event logs.
- Ability to forward logs and integrate them into a SIEM solution.
- Proficiency in creating attack maps to track real-time hacker activity.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used

- Micorosft Azure
- Microsoft Sentinel SIEM solution
- Window's "Remote Desktop Connection" app

## Steps

1. create a Free Azure subscription from the following link: https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account
If you have previously created an account with your credit card information, you may need to just create a paid subscription. If you do so, just keep in mind the cost of your resources, and make sure to shut down or delete your resources when you are done with this lab.
Once you have created your subscription, you should be able to log in at https://portal.azure.com 
2. Create the resource group and network for the honeypot
Go to https://portal.azure.com and search for and select resource group. In the resulting page, click “create”

![image](https://github.com/user-attachments/assets/ec0abae4-61c2-48da-85d9-89cf76aae361)

On the next screen you will be prompted to choose what subscription the resource group is billed under, a name, and a region. Leave the subscription field as is if using the free subscription, and pick an applicable name for the group. For the region, choose the area closest to your actual location. Once done, select “Review + Create” and then “Create”

![image](https://github.com/user-attachments/assets/b548e82a-cc08-4e19-a0b1-85ccfd56e901)

Next the Virtual network. Click on the main search bar and search up “virtual Network”. Like the resource group, click “create virtual network”, and set a name for your VN, making sure it is under your resource group and in the same region. Once done, hit “Review + Create” and “Create”(normally you will want to carefully review the IP address range and security settings, but in this case we are using the default settings)

![image](https://github.com/user-attachments/assets/466f1128-193b-4081-bd1b-28e0a0780cd6)

To confirm everything is setup correctly, go to your resource groups using the search, and if you open your resource group, the virtual network should be within it

![image](https://github.com/user-attachments/assets/01acef55-b2fd-42e8-bdb6-b25c079d13e3)

Next, create a virtual machine. Search up “virtual machines” in the main search bar, click “Create”, and then click “Azure Virtual Machine”
In the creation settings, set the resource group to your created group, under name set a custom name(keep in mind that this name is what the attackers will see, so try to set it as something inconspicuous, and not “HONEYPOT”). For the image, use windows 10(you will typically only be limited to one version of windows 10 for the free subscription). Under image, leave it at the standard option. Depending on your region, you may have to select a specific zone to use a windows 10 image, in which case Azure will give you a warning to do. Follow whichever zone setting azure prompts you for.

![image](https://github.com/user-attachments/assets/6b5d5f18-c4ca-418f-b04c-495caee469b9)

Next, set a simple username and password, and keep track of them somewhere(if you forget it, I will show how to reset the password later). Under licensing, select whatever checkboxes it shows

![image](https://github.com/user-attachments/assets/ceb214e4-f0a2-44b4-a614-257cf6af798d)

Once complete, hit next at the bottom to go to disks, and next again to go to networking. Under networking, select your virtual network and subnet that was created earlier(it should normally default to it). Select the check next to “Delete public IP and NIC when VM is deleted” to save time with the cleanup later

![image](https://github.com/user-attachments/assets/091f56d8-83ad-4dcc-a7ba-d64559fb6fc5)

Once done, hit next to go to management, then next to go to monitoring. Undermonitoring, disable the boot diagnostics, then select “Review + Create”

![image](https://github.com/user-attachments/assets/136bb05f-9ea0-43e9-9b67-1e71180c4e94)

Once azure is done loading and validating, hit “Create”. This may take a few minutes.
Once you are brought to the following screen, the virtual machine has been set up:

![image](https://github.com/user-attachments/assets/a9c0d370-551a-4eb5-852d-ec51c476f8f0)

3. Set up security settings
In Azure, go to resource groups, and select your created resource group. You should now see your virtual machine, along with a few other items named after it. Locate the network security group(which will be named “[name of your vm]-nsg”), and double-click it to open its settings. It should now look something like this:

![image](https://github.com/user-attachments/assets/9caa0cd8-f74d-4c59-a2fe-dc9910e4f017)

From here, we will need to create a custom rule to allow any inbound traffic(do NOT do this with an actual computer). First delete the first rule at the top to allow inbound RDP connections by clicking the trash can on the right. Next on the left select Settings > Inbound security rules, and then at the top left select “Add”.
For this rule, set the source as “Any”, the source port ranges as “*”(meaning all), leave the service as Custom, set the destination port ranges as “*”, Protocol as “Any”, Action as “Allow”, and leave the priority and name as is. Once finished, your settings for the rule should look like the following:

![image](https://github.com/user-attachments/assets/2511e0c4-c28a-4358-99e5-07e17272f992)

Once complete, hit “Add” top add the rule.
4. Disable the windows firewall within the VM
In azure at the top, search for and select “Virtual Machines”. In the VM menu, select your virtual machine to view its information, and then copy the public IP address. 

![image](https://github.com/user-attachments/assets/e0b6994f-a8d5-4a16-aa8e-fdc5a5195f8d)

(if using a Mac, on these next steps download and use the app “Windows App” to remote connect). On a windows computer, open the app “Remote Desktop Connection”, and enter in the public IP address of your VM to connect

![image](https://github.com/user-attachments/assets/9833db19-37b8-40af-8b6b-f6d8e323c0b0)

*You may get a warning about this connection not having a signed certificate, if so simply choose to continue and connect*
**If at any point you forget your password for the VM, you may delete it in the VM settings window at the top, or on the tabs on the left if you select Help > Reset Password, you should then be brought to a screen where you can assign a new password to the account on the VM:

![image](https://github.com/user-attachments/assets/ac2e60e6-1094-44a0-8638-9813b757f8d3)

If you get prompted for privacy settings in the virtual machine, select no to all options, and then hit “Accept”

![image](https://github.com/user-attachments/assets/6a9aff3e-4d0c-4b95-afdc-e7fbfd6244c7)

Once inside of the virtual machine, click on the search bar in the bottom left and type in “wf.msc”

![image](https://github.com/user-attachments/assets/db6f8fb2-4dd7-4f33-968b-f248cb134f76)

Once inside, select “Windows Defender Firewall Properties” 

![image](https://github.com/user-attachments/assets/08cf8369-03ba-4375-8203-a6703b8e31a0)

Once in the properties, in the first 3 tabs “Domain Profile”, “Private Profile”, and “Public Profile”, select “off” for the firewall state

![image](https://github.com/user-attachments/assets/938357d8-ed7e-4844-b2a6-1b789ff71648)

Once all are off, hit “apply” and then “okay”. To confirm that the firewall has been updated, attempt to ping your virtual machine using your command prompt, powershell, or terminal(depending on your operating system). If you get bytes returned, then the firewall is successfully off.

5. Log data setup
Disconnect from your virtual machine, and intentionally try to sign in incorrectly 3 times with the username “employee” and random data for the password. After putting in an incorrect user and password, properly sign into your virtual machine. 
Once connected, type into the search bar and open “event viewer”. Expand the app and on the left select Windows Logs > Security

![image](https://github.com/user-attachments/assets/ffb80942-6572-4a56-8596-b6fbfb448766)

In the detailed pane in the middle, you should notice a lot of “Audit Success” and “Audit Failure” logs. We are looking for events with the event id 4625, which on windows represents a logon failed

Back in Azure, use the main searchbar to lookup “Log Analytics” and select Log Analysts Workspaces, and then choose to create one. Like with the previous Azure resources, in it’s settings assign it to your resource group, give it a general name, and ensure it is assigned to the region near you.

![image](https://github.com/user-attachments/assets/5708c086-9944-41c1-ae42-aa4b63e95df9)

Once finished, select “Review + Create”
Once the repository is created, in Azure lookup and select “Sentinel”. Once in the Sentinel section, select “Create”, then select your log analytic workspace. After a few minutes, a connection should be created from the Sentinel SIEM and our log analytics workspace.

7 connection between VM and log analytics workspace
To make the connection, in the Sentinel view on the left select Content Management > Content Hub

![image](https://github.com/user-attachments/assets/963a6e3b-7d77-4208-8e53-b3ed9bb89b98)

In the content hub, narrow the results by typing in the search bar “security events” and hitting enter

![image](https://github.com/user-attachments/assets/2d22ef6f-b918-45b4-b50c-27d51bc6b46d)

From here, select the checkbox next to Windows Security Events, and in the bottom right hit “install”(this will take a few minutes). Once installed, in this same view there should now be the option for “Manage” in the bottom right, select it.

On this next menu, select the checkbox next to “Windows Security Events via AMA”. This will open a window on the right, in this windows at the bottom select “open connector page”

![image](https://github.com/user-attachments/assets/34862864-e141-4f40-b99b-d35be91d9a72)

This will open the settings for this tool. In the bottom middle now, select “Create date collection rule”

![image](https://github.com/user-attachments/assets/b81834e1-f2ca-4d20-ac12-675fcdd718e7)

This will create another object within our resource group, that will allow the Virtual Machine to forward logs into our log analysis workspace, which will let us then access the logs inside of Windows Sentinel. Within the settigns for the rule, give it an appropriate name, assign it to your resource group, and hit “Next” at the bottom

![image](https://github.com/user-attachments/assets/ba021c85-ddbf-4390-b8e1-9c0cd174e19c)

In the resource tab, no click the arrow next to “Azure subscription 1”, then the arrow next to your resource group, and you will now see your Virtual Machine. Click the checkbox next to your Virtual Machine, and then hit “Next” 

![image](https://github.com/user-attachments/assets/008ee387-fe08-49e0-8b4c-6637f7a995d5)

In the Collect tab, leave it selected at “All Security Events”, hit “Review + Create”, and finally hit “Create”
This rule can take a bit to apply to the Virtual Machine, but to check its status, in Azure search for and open Virtual Machines, select your created Virtual Machine, on the tab on the left select Settings > Extensions + applications, and if set properly, there should be an item named “AzureMonitorWindowsAgent”

![image](https://github.com/user-attachments/assets/3fb29ef3-0476-413b-8267-b971f0525b1c)

Once our rule is installed, it may take some more time to apply to the Virtual Machine, but you will know that it is set properly once the status is set to “Provisioning succeeded” like in the above screenshot.

Once the new rule is fully applied, in Azure go back to the Log Analytics Workspaces, select your created workspace, and on the pane on the left select Logs.

![image](https://github.com/user-attachments/assets/26d3ef91-18ec-4cae-b181-0b7e312eea09)

Azure will give you 2 pop-up tutorials, go ahead and close out of both. You should now be brought to a screen that shows “New Query 1” in the top right. To briefly confirm that our workspace is gathering logs from the VM, first click on the drop down on the right that is set to “Simple mode”, and change it to “KQL mode”. This will open a new menu in the middle., in this middle menu enter “SecurityEvent”, and then click the blue “run” button in the top left

![image](https://github.com/user-attachments/assets/eb67390f-488a-4aa7-970b-a077c15fd523)

Provided that the VM is connected properly, you should now see the event logs we reviewed earlier in the Results tab below.

![image](https://github.com/user-attachments/assets/5a938123-6bab-4f29-835f-e1e185dafafd)

If you don’t see logs yet don’t sweat! If you performed the checks from earlier in this walkthrough, the log rule created earlier may need more time still to set up, you may simply need to wait 20 minutes.
9. Query our log repository with KQL
We will next configure the log workspace to query the failed logon attempts for the source IP of the user, determine a general location of that user based on their IP, and then use that to make a map of the incoming failed logon attempts.
To do so, first download this CSV of generalized IP-to-geolocation data: https://drive.google.com/file/d/13EfjM_4BohrmaxqXZLB5VUBIz2sv9Siz/view?usp=sharing 
After downloading the file, go to Azure and search and open Microsoft Sentinel, on the pane on the left select Configuration > Watchlist, and at the top select “New”

![image](https://github.com/user-attachments/assets/25b4b045-69d0-415d-9b31-af8586dadee6)

*For the Watchlist all of the info needs to be specific, so be certain to enter it exactly as I say*

In watchlist, for the name enter “geoip”, and for the alias enter “geoip”, and then hit “Next”

![image](https://github.com/user-attachments/assets/e55605c5-9e96-4b7b-8cf5-061b3225f378)

In the source tab upload the file I pointed out earlier, the “geoip-summarized.csv”. For the SearchKey click the dropdown and select “network”

![image](https://github.com/user-attachments/assets/ea1db63e-faf5-4b34-8bf4-c640a7a7f9ad)

Finally hit “Review + Create” and “Create”. You will be automatically brought back to the Watchlist tab, however it will take a few minutes to upload the document into Sentinel. However as long as you see “geoip” under “My Watchlists”, it is working correctly! If you click on geoip it will show you its current status. When it is fully finished, there should be about 55k Watchlist items registered.
Once the items are gregistered, go back into Log Analytics Workspaces, select your created workspace, and on the pane on the left select Logs to open the query panel.To test if the CSV was added as a success, run the KQL query
 “SecurityEvent
_GetWatchlist("geoip")”
If done successfully, you should get a results page with the columns “cityname”, “countryname”, and others

![image](https://github.com/user-attachments/assets/d4384a84-b6d7-4f8b-ac99-d6752299e57b)

If you see the columns, success!
10. Set up geomap in Sentinel
In Azure, search up and open Microsoft Sentinel, In Sentinel, select your workbook, and in the left pane select Threat Management > Workbooks

![image](https://github.com/user-attachments/assets/5ac0a8f7-3af7-4864-ab07-e23fd44b54d1)

From here, select “Add Workbook” at the top. From this new menu, select “Edit in the top right

![image](https://github.com/user-attachments/assets/1c08f6c2-82ab-4e99-af7d-4c01f4289de1)

In the prebuilt workbook, click on the ellipse(the “…”) in the right corner of the 2 prebuilt items, and select “Remove” on both(you may have to hit “yes” to confirm)

![image](https://github.com/user-attachments/assets/b4f9a5c7-01dd-417d-9a9c-d09e3c402c8c)

From here, select “Add” in the top left, and in the drop-down menu select “Add query”. In the query menu, click on the “Advanced Editor” tab, and delete the prebuilt info, and paste the following data:
{
	"type": 3,
	"content": {
	"version": "KqlItem/1.0",
	"query": "let GeoIPDB_FULL = _GetWatchlist(\"geoip\");\nlet WindowsEvents = SecurityEvent;\nWindowsEvents | where EventID == 4625\n| order by TimeGenerated desc\n| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)\n| summarize FailureCount = count() by IpAddress, latitude, longitude, cityname, countryname\n| project FailureCount, AttackerIp = IpAddress, latitude, longitude, city = cityname, country = countryname,\nfriendly_location = strcat(cityname, \" (\", countryname, \")\");",
	"size": 3,
	"timeContext": {
		"durationMs": 2592000000
	},
	"queryType": 0,
	"resourceType": "microsoft.operationalinsights/workspaces",
	"visualization": "map",
	"mapSettings": {
		"locInfo": "LatLong",
		"locInfoColumn": "countryname",
		"latitude": "latitude",
		"longitude": "longitude",
		"sizeSettings": "FailureCount",
		"sizeAggregation": "Sum",
		"opacity": 0.8,
		"labelSettings": "friendly_location",
		"legendMetric": "FailureCount",
		"legendAggregation": "Sum",
		"itemColorSettings": {
		"nodeColorField": "FailureCount",
		"colorAggregation": "Sum",
		"type": "heatmap",
		"heatmapPalette": "greenRed"
		}
	}
	},
	"name": "query - 0"
}
Once pasted in, click “Done Editing” in the bottom left. If set correctly, you should have a basic map configured showing where your queries are coming from, something like this:

![image](https://github.com/user-attachments/assets/cc44bc79-93fd-4706-b84c-c4fb3e53af9e)

Next, make sure to click on the floppy disk icon towards the top, and give your workbook a title, and assign it to your resource group, and then hit “Save As” at the bottom

![image](https://github.com/user-attachments/assets/1ac52ad9-0211-4c6f-ac7d-3c20aded356a)

And with that, our Lab is complete! You now have an open VM honeypot that is collecting data from attackers around the world, and can show it off in the workbook. I’d recommend leaving the VM running for a few days to get a lot of data.
