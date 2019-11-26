# BMC Remedy and Helix Incident Flow Designer Integration
Notify on-call response teams when critical incidents are reported in Remedy or Helix. With the xMatters and BMC Remedy On-Premise or Helix On-Demand (using the xMatters Agent) closed-loop integration, the on-call members of resolver teams are automatically notified via multiple communication channels. When the recipient responds, notes are added to the incident work log and specific incident actions may take place depending on the response.

# Pre-Requisites
* Version 19.08 or later of Remedy (On-Premise or On-Demand) or Helix (On-Demand)
* Account in Remedy or Helix
* xMatters account - If you don't have one, [get one](https://www.xmatters.com)!

# Files
[BMCRemedyandHelixIncident70.zip](BMCRemedyandHelixIncident70.zip) - download this Workflow to get started  
[BMCRemedyHelix_defn.zip](BMCRemedyHelix_defn.zip) - download this zip file containing the BMC Remedy/Helix workflow definition files

# How it works
Remedy/Helix triggers one of the xMatters filters as part of the integration. The filter POSTs the Remedy/Helix Incident ID to xMatters, and in turn xMatters uses a Remedy/Helix REST API to get the incident properties and subsequently creates the xMatters Event targeted to the assigned resolver Group.

The notified resolver responds with Assign to me - to take ownership of the incident or Escalate - to escalate to the next resource in the on call schedule.

The closed loop integration annotates the incident work info with xMatters event status, notification delivery status, user responses and user comments. Additionally, an Assign to me response assigns the user to the incident and updates the incident status to In Progress.

# Installation 

## xMatters set up
### Create a REST integration user account

<kbd>
  <img src="media/xMRESTUser.png">
</kbd>  

### Install xMatters Agent for integration with Remedy On-Premise
* On the Remedy Mid-Tier Server where the xMatters Agent is to be installed, in xMatters on the *Workflows* page in the left navigation bar click *Agents*
* Select the OS of the server from the drop-down list
* Click *Download this version*
* Choose a proxy configuration
* Follow the steps shown in Step 3
* The xMatters Agent will be installed on the server and the service will automatically start

### Import the Workflow
* Import the **BMC Remedy and Helix | Incident | 7.0** (BMCRemedyHelixIncident70.zip) Workflow     https://help.xmatters.com/ondemand/xmodwelcome/workflows/manage-workflows.htm

### Assign permissions to the Workflow and Form  
* On the *Workflows* page, click the Edit drop-down menu for the **BMC Remedy and Helix | Incident | 7.0**  then select *Editor Permissions*
* Add any users, groups and/or roles to have editor permissions to this workflow
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then select *Forms*
* Click the *Web Service* drop-down menu for the **Incident Alerts** form
* Select *Sender Permissions* then add the REST integration user

### Configure List Property Values  
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then select *Properties*
* Verify/Edit the following list property values:  
   Company  
   Contact Sensitivity  
   Escalated  
   Impact  
   Priority  
   Reported Source  
   SLM Status  
   Service Type  
   Status  
   Status_Reason  
   Urgency  
   VIP

### Configure Integration Builder Constants and Endpoints  
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then select *Integration Builder*
* Click *Edit Endpoints*
* For the xMatters endpoint, in *Assign Endpoint* add the REST integration user then *Save Changes*
* For the Remedy JWT endpoint, type the Base URL for the Remedy environment then *Save Changes*
* For the Remedy Token endpoint, type the Base URL for the Remedy environment, type the integration user in Remedy in *Username*, click *Change Password* then type the password, select *Preemptive* and then *Save Changes*
* For the xMatters Basic endpoint, type the Base URL for the xMatters environment, type the REST integration user in xMatters in *Username*, click *Change Password* then type the password, select *Preemptive* and then *Save Changes*
* *Close* Edit Endpoints  
* Click *Edit Constants*, then edit these constants:
   
| Constant                        | Description                                                              |
|:------------------------------- |:------------------------------------------------------------------------ |
| REMEDY_FQDN                     | Fully qualified domain name of the Remedy/Helix Mid-Tier Server          |
| REMEDY_OPT_SIMPLE_GROUP_NAME    | true to use simple group names or false to use Company*Org*Group         |
| REMEDY_SERVER_NAME              | Remedy logical server name                                               |
| USER_REMEDY_PASSWORD            | Password for the integration user in Remedy used for JWT authentication  |
| USER_REMEDY_USERNAME            | Username for the integration user in Remedy used for JWT authentication  |
| XMATTERS_INCIDENT_EVENT_IB_PATH | Inbound Integration path (Basic Authentication) to Step 02               |
| XMATTERS_INCIDENT_IB_FLOW_PATH  | Inbound Integration path (URL Authentication) to Step 01                 |

### Get the XMATTERS_INCIDENT_EVENT_IB_PATH  
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then click *Integration Builder*
* Click the *3 Configured* link for *Inbound Integrations*
* Click the *Step 02 | Create Event | Incident Alert | Flow* link
* Scroll to the **How to trigger the integration** section then click *Select method* and *Basic Authentication* and click *Copy* to copy the URL
* Be sure to remove everything before */api/integration* after pasting in the Constant

### Get the XMATTERS_INCIDENT_IB_FLOW_PATH  
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then click *Integration Builder*
* Click the *3 Configured* link for *Inbound Integrations*
* Click the *Step 01 | Inbound Request | JSON* link
* Scroll to the **How to trigger the integration** section then click *Select method* and *URL Authentication* 
* In *Authenticating User* begin typing the username for the REST integration user in xMatters and select the user
* Click *Copy* to copy the URL
* Be sure to remove everything before */api/integration* after pasting in the Constant

### Verify Flow Designer steps
Some Flow Designer steps are configured with a *Run Location* of either *Cloud* for Remedy On-Demand or Helix On-Demand or *xMatters Agent* for Remedy On-Premise 
* On the *Workflows* page, click the **BMC Remedy and Helix | Incident | 7.0** then click *Flows*
* Click **Incident Alerts Workflow**
* Verify the *Run Location* for all steps on the Flow Designer canvas with labels as identified below:  
   Add Assignee | Incident | JWT Auth  
   Add Work Info | Incident | JWT Auth  
   Get Incident | JWT Auth  
   Remedy API Token | Acquire  
   Remedy API Token | Release  

## Remedy/Helix set up
Configuring BMC Remedy or Helix to integrate with xMatters requires the following steps:

* Import the workflow definition files
* Configure filters
* Configure the ITSM user
* Disable automatic assignments

### Importing workflow definition files
* Log in to the BMC Remedy Developer Studio, and then select **File** > **Import**
* Select **BMC Remedy Developer Studio** > **Object Definitions**, and then click **Next**
* Select the AR System server into which you want to upload the integration objects, and then click **Next**
* Do one of the following:  
      Type in the location of the `xm_foundation_8_1.def` file  
      Click the Browse button to the right of the text field and navigate to the location of the `xm_foundation_8_1.def` file. Select the file, and then click **Open**.  
* Click **Next**  
      If you have already imported a workflow definition file, ensure that you select the Replace Objects on the Destination Server check box (do not select the other check boxes), but note that any changes you have made to those objects will be lost. If you are sure the changes you made are necessary for your installation, you will be required to re-apply those changes to the new version of the files being imported unless you applied those changes to overlay objects.  
* Repeat the above steps to import the `xm_incident_8_1.def` file.  
      Note this file must be imported after the foundation file.  
Click **Finish**

### Configuring filters
The integration includes a filter and an escalation that use the Set Fields action to consume a web service; these objects need their endpoints changed to the address of the Step 01 | Inbound Request | JSON inbound integration for On-Demand or the xMatters Agent for On-Premise.  
Filter: XM:EI:EventInjection_100  
Escalation: XM:Event Injection Retry

#### For Remedy/Helix On-Demand, get the endpoint URL
Follow the instructions above for *Get the XMATTERS_INCIDENT_IB_FLOW_PATH*. Paste the full URL in the Remedy/Helix filter.

#### For Remedy On-Premise, get the endpoint URL for the xMatters Agent
* On the Workflows page, click the **BMC Remedy and Helix | Incident | 7.0** then select Integration Builder
* Click the *3 Configured* link for Inbound Integrations
* Click the *Step 00 | Incoming Remedy | xM Agent* link
* In **Integration Settings Step 1**, select *xMatters Agent* in *Location* and select the appropriate Agent
* In Step 3, select *URL Authentication*
* Scroll to the **How to trigger the integration** section then click *Select method* and *URL Authentication* 
* In *Authenticating User* begin typing the username for the REST integration user in xMatters and select the user
* Click *Copy* to copy the URL
* Paste the full URL as the endpoint in the Remedy/Helix filter

### Configuring ITSM user
The integration requires a dedicated ITSM user to interact with incidents.

#### Create an ITSM user
First, create a new ITSM user with the Incident Master role in BMC Remedy/helix; the user does not need to be Support Staff.

<kbd>
  <img src="media/RODITSMUser.png">
</kbd>

**Note: If you specify a Login ID of "xmatters" for this ITSM user, you can skip the following two update steps.**

#### Update the filter qualification
The XM:Incident_Re-Assigned_899 filter contains the following qualification criteria: `($USER$ != "xmatters")`  

This qualification prevents the integration from sending a second notification based on an incident's assignment changing because of a user response to an earlier notification. Replace `xmatters` with the name of the ITSM user created in Step One.

<kbd>
  <img src="media/RODUpdateFilterQual.png">
</kbd>

#### Update the default assignee
The out-of-box permissions allow the Submitter and Assignee (and BMC Remedy administrators) to search instances of the XM:Event Injection form. This allows users who modify incidents to see the corresponding XM:Event Injection instance for their update. To allow the ITSM user to also see all the Event Injection forms, modify the default value for the Assigned To field to the ITSM user you created.  

<kbd>
  <img src="media/RODUpdateDefaultAssignee.png">
</kbd>

### Disabling automatic assignment
To allow xMatters to control assignments, you must turn off the automatic assignment feature in BMC Remedy.

**Note: To perform this step, you will need to login as a user with Administrator permission.**

* Log in to the BMC Remedy Mid Tier web server.
* Click **Applications**, and then click the **Application Console** left-menu item.
* Click **Application Administration Console**.
* In the new window, expand **Incident Management**, and then expand **Advanced Options**.
* Select **Rules**, and then click **Open**.
* Search for all existing "Configure Incident Rules".
* For each existing rule, do the following:  
      Select the rule, and in the **Assignment Process** drop-down list, select **(clear)**.  
      Click **Save**.  


# Testing

## Triggering a notification
To trigger a notification, create a new incident with a priority of High or Critical in BMC Remedy, and assign it to
a user or group that exists in both BMC Remedy and xMatters:  

<kbd>
  <img src="media/RODTriggerNotification.png">
</kbd>

## Responding to a notification
In the following example, the notification is received on an Apple iPhone, but the process is similar for all devices.  

* Notifications appear in the application Inbox  

<kbd>
  <img src="media/RODiPhone01.png" width=236 height=420>
</kbd>  

* Opening the notification displays the details  

<kbd>
  <img src="media/RODiPhone02.png" width=236 height=420>
</kbd>  

* After viewing the details, either click the respond (blue return arrow) icon at the top or scroll to the bottom of the notification  

<kbd>
  <img src="media/RODiPhone03.png" width=236 height=420>
</kbd>  

* Tap the desired response, then tap **Respond now** or **Respond with comment**  

<kbd>
  <img src="media/RODiPhone04.png" width=236 height=420>
</kbd>  


# Troubleshooting
If an xMatters notification was not received you can work backwards to determine where the issue may be:  
* Review the xMatters Reports tab and the specific [Event Log](https://help.xmatters.com/ondemand/installadmin/reporting/eventlogreport.htm)  
* If no Event was created, review the [xMatters Workflows Flow Designer Activity Panel](https://help.xmatters.com/ondemand/xmodwelcome/flowdesigner/activity-panel.htm)  
* If no activity was recorded, review the Remedy/Helix logs for a POST to xMatters
