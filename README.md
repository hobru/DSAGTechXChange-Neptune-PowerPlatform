# DSAGTechXChange - No-Code/Low-Code Challenge

Hands-on Session as part of the [DSAG TechXChange 2024](https://dsag.de/wp-content/uploads/2023/12/Programm_und_Agenda_DSAG-TechXchange.pdf)

## 🥅 Goal

In this session, we want to show how easily it is to combine the strength of the Neptune DXP to build UI5 apps in a low-code approach and combine this with a Power Automate flow to send approval notifications to Teams. 

As a result, you will have a simple App, that allows you to trigger an Approval for a SAP Sales Order, then see the notification in Teams and approve it directly from there. 

## Logistics

| Neptune User | Neptune	Power Platform & Teams User |
|--------------|-----------|
|DSAG1|alans@CRM625261.OnMicrosoft.com	|
|DSAG2|aliciat@CRM625261.OnMicrosoft.com|	
|DSAG3|allieb@CRM625261.OnMicrosoft.com|	
|DSAG4|amya@CRM625261.OnMicrosoft.com	|
|DSAG5|annew@CRM625261.OnMicrosoft.com	|
|DSAG6|carlosg@CRM625261.OnMicrosoft.com	|
|DSAG7|christag@CRM625261.OnMicrosoft.com|	
|DSAG8|danj@CRM625261.OnMicrosoft.com	|
|DSAG9|davids@CRM625261.OnMicrosoft.com	|
|DSAG10|dianep@CRM625261.OnMicrosoft.com|	
|DSAG11|ericg@CRM625261.OnMicrosoft.com	|
|DSAG12|gregw@CRM625261.OnMicrosoft.com	|
|DSAG13|jamier@CRM625261.OnMicrosoft.com|	
|DSAG14|jeffh@CRM625261.OnMicrosoft.com	|
|DSAG15|juliani@CRM625261.OnMicrosoft.com|	
|DSAG16|karenb@CRM625261.OnMicrosoft.com|	
|DSAG17|kellyk@CRM625261.OnMicrosoft.com|	
|DSAG18|mollyc@CRM625261.OnMicrosoft.com|	
|DSAG19|reneel@CRM625261.OnMicrosoft.com|	
|DSAG20|sanjays@CRM625261.OnMicrosoft.com	|
|DSAG21|spencerl@CRM625261.OnMicrosoft.com|	
|DSAG22|svenm@CRM625261.OnMicrosoft.com	|
|DSAG23|veronicaq@CRM625261.OnMicrosoft.com|	
|DSAG24|williamc@CRM625261.OnMicrosoft.com|	
|---|- Careful, the Domain is changed for the next ones! - |
|DSAG25|jeffh@CRM433983.onmicrosoft.com|	
|DSAG26|juliani@CRM433983.onmicrosoft.com|	
|DSAG27|karenb@CRM433983.onmicrosoft.com|	
|DSAG28|kellyk@CRM433983.onmicrosoft.com|	
|DSAG29|gregw@CRM433983.onmicrosoft.com|	
|DSAG30|ericg@CRM433983.onmicrosoft.com|	
	


Password will be provided during the workshop. 

## 🤝 Neptune DXP

Login to the Neptune Cockpit

https://neptune-academy.neptune-software.cloud/cockpit.html

- Username: dsagxx (for xx user your group number e.g. dsag1)

> [!NOTE]
>🏋🏽You can easily download a free Trial here
> - [Neptune Software Free Trial](https://www.neptune-software.com/free-trial/)

### Neptune App Designer

We will start by creating a new Neptune app with the App Designer.

- Download the following planet9 file from https://github.com/hobru/DSAGTechXChange-Neptune-PowerPlatform/blob/main/dsag_tech_exchange_salesorders.planet9 and store it locally.

- From the Neptune Cockpit start the `App Designer` Tool by clicking on the tile
  ![App Designer](images/neptune-app-designer-tile.jpg)

- In the dialog select "New from file"
  ![New](images/neptune-appdesigner-new-from-file.jpg)

- Select the planet9 file you downloaded before from this GitHub repository
  ![Browse](images/neptune-browse.jpg)

- Fill in a name for the app like salesorder_approval_xx (For xx use the number of the group to make the app unique)
  ![Open](images/neptune-newapp.jpg)

### Connect OData Service + REST Services

In the Cockpit open the OData Source Tool and search for the SalesOrders service

![OData](images/neptune-odata-service.jpg)

This is the service we will use in our application to connect to our S/4 HANA system and will be used to retrieve the Sales Orders.

Return to the App Designer https://neptune-academy.neptune-software.cloud/appdesigner.html and open the Application created before.


- Drag the ODataSource from the tree to the Resources in the Application structure and rename it `SalesOrdersOData`
![Connect OData](images/neptune-app-designer-odata.jpg)

- Select the `SalesOrdersOData` service and on the right of the screen select the oData source `SalesOrder` from the list
![Neptune](images/neptune-app-designer-odata2.jpg)

### Test the app

Press the `Activate` button on the top of the screen and press `Run` to start the application again. Check if the Sales Orders are displayed in the list and you can click an item from the list to see the details.

![Sales Orders List](images/neptune-sales-orders-list.jpg)

## 🤝 Power Platform

Now that we have the Neptune app running, let's switch over to Power Automate. For this hands-on session, we have created an environment for you!

> [!NOTE]
>🏋🏽You can easily request a free M365 and Power Platform environment via these links 
> - [M365 Developer Program](https://developer.microsoft.com/en-us/microsoft-365/dev-program/)
> - [Power Platform Developer Program](https://powerapps.microsoft.com/en-us/developerplan/)



###  Create your first Power Automate Flow

- Go to https://make.powerautomate.com/
  ![Power Automate](images/01-PowerAutomate.png)

> [!NOTE]
> Normally you would group and package all your flows in a solution. However, since we are going to only create one flow in our scenario, we create our flow in the default solution

- Click on `+ Create`
  ![Power Automate](images/01-PowerAutomate.png)

- Select `Instant Cloud Flow`
  ![Power Automate](images/02-CreateInstantCloudFlow.png)

- Give the flow a name, e.g. `Receive call from SAP via Neptune`
  ![Power Automate](images/03-EnterFlowName.png)

- Select `When an HTTP request is received` from the very end of the list of available triggers, then click on `Create`
  ![Power Automate](images/04-HTTPRequest.png)

- In the designer screen click on `manual` and change the following properties on the left hand side:
> [!NOTE]
> You need to click on Show all under `Advanced parameters` to see all required settings

* Who Can Trigger The Flow: `Anyone`
* Method: `POST`

![Power Automate](images/05-ChangesToManualTrigger.png)


* Click on `Use sample payload to generate schema` and paste the following JSON sample (this is what is sent from Neptune to our Power Automate flow). Then click on `Done`
```
{
    "messageID": 111,
    "messageText": "Hello, World!"
}
```

![Power Automate](images/06-SamplePayload.png)

- Now add a new step to send an Adaptive Card to Teams. Click on the `+` sign under our trigger step "manual" and then `Add Action`
  ![Power Automate](images/07-AddAction.png)

- In the `Add an action` search filed, enter `Teams wait` and select `Post adaptive card and wait for a response`
  ![Power Automate](images/08-AddTeamsAction.png)

- Click on `Sign-in` to connect the Power Automate flow with Teams. Sign-in with your Microsoft 365 user.
  ![Power Automate](images/09-TeamsSignIn.png)

- Select the Channel and Teams where the Adaptive Card should be posted. 
* Post In: `Channel`
* Team: `Contoso`
* Channel: <Please select the User that was assigned to you, e.g. `DSAG1`>
  ![Power Automate](images/10-SelectTeamsChannel.png)

- Paste the content for the adaptive card from below in the Message field
  ![Power Automate](images/11-TeamsAdaptiveCard.png)

```json
{
  "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
  "type": "AdaptiveCard",
  "version": "1.2",
  "body": [
    {
      "type": "TextBlock",
      "size": "Medium",
      "weight": "Bolder",
      "text": " Approve SAP Workflow @{triggerBody()?['messageID']} via  Neptune",
      "horizontalAlignment": "Center",
      "wrap": true,
      "style": "heading"
    },
    {
      "type": "TextBlock",
      "text": "@{triggerBody()?['messageText']}",
      "wrap": true
    },
    {
      "type": "Input.Text",
      "id": "comment",
      "label": "Comment"
    }
  ],
  "actions": [
    {
      "type": "Action.Submit",
      "title": "Approve"
    },
    {
      "type": "Action.Submit",
      "title": "Reject"
    }
  ]
}
```

> [!NOTE]
> If you want to desing your own adaptive card, you can go to https://www.adaptivecards.io/designer/

- Save the Power Automate Flow
  ![Power Automate](images/12-SavePA.png)

- Click on the `manual` trigger action and `copy the HTTP POST URL`. This is the URL that will be used on the Neptune side to trigger the flow.
  ![Power Automate](images/13-CopyHTTPUrl.png)

> [!NOTE]
> You could now use Postman (or another REST Client), to call the URL with a sample payload


## 🤝 Switch back to Neptune DXP

https://neptune-academy.neptune-software.cloud/cockpit.html

### API Designer

In the Neptune Cockpit select the `API Designer` tile and open it.

Here we will add a new API for the Power Automate Trigger. This API will be used in the Neptune Application.


- Click on the `Add` button to create a new API
![new API](images/neptune-add-api.jpg)

- Fill in a unique name e.g. `DSAGTechExchangePowerAutomateTrigger_xx` where `xx` is your group number and copy only the first part of the URL until `/paths` from Power Automate as `Endpoint`
eg. `https://prod-150.westeurope.logic.azure.com:443/workflows/9a92351242184f7d9fc6f322cddf9df3/triggers/manual/paths`
![API Designer Add](images/neptune-api-designer.jpg)

- Enable the checkbox for `Enable Proxy` and `Use in App Designer & App Editor`
![API Designer checkbox](images/neptune-api-designer2.jpg)

- Go to the `Operations` tab and press the `+` button and enter the following values
  - Path: `/invoke`
  - Method: `POST`

- Go to the Request tab and enter the following parameters.

> [!IMPORTANT]  
> Please use the correct values from the copied URL from Power Automate, otherwise you will trigger the wrong endpoint later. Especially the `sig` parameter is important since that is unique for your Power Automate Flow.

  - api-version: `2016-06-01`
  - sp: `%2Ftriggers%2Fmanual%2Frun`
  - sv: `1.0`
  - sig: `_UO6mNVAhQwYaFEdWBVsSZq_bolYF8Ee9iUL1BHvUK4`
  
  ![API Designer parameters](images/neptune-api-designer3.jpg)


- Press `Save` to store the API Endpoint

### App Designer

Now we can use this API endpoint in our application. Open the App Designer again https://neptune-academy.neptune-software.cloud/appdesigner.html

- In the Tree library search for `RestAPI` and drag it under resources 
![App Designer RestAPI](images/neptune-appdesigner-restapi.jpg)

- Rename the `RestAPI` to `TriggerPowerAutomate`
- On the right side press the select box next to `Rest API:`
![App Designer RestAPI](images/neptune-appdesigner-restapi2.jpg)
- In the search dialog search for your `DSAGTechExchangePowerAutomateTrigger_xx` and select the `POST` method.
![App Designer RestAPI](images/neptune-appdesigner-restapi3.jpg)

Now we will implement some code behind the Trigger Button.

Search for the `ButtonTrigger` in the UI tree and select it

![Trigger Button](images/neptune-trigger-button.jpg)

On the right side of the screen in the panel click the button next to the press function

![Trigger press](images/neptune-trigger-button-press.jpg)

Add the place where the `TODO` comment is you can add the API call by using the code snippets functionality.

- Right-click in the Javascript file at the place you want to put the code and select the `Code snippets` option
- Collapse the API option. 
- Select `TriggerPowerAutomate` and press `Copy`


The code similar to below will be copied. The values might slightly differ because the parameters for each user are different because of the different Power Automate API Endpoints
```js
var options = {
  parameters: {
    "api-version": "2016-06-01", // Optional
    sp: "%2Ftriggers%2Fmanual%2Frun", // Optional
    sv: "1.0", // Optional
    sig: "_UO6mNVAhQwYaFEdWBVsSZq_bolYF8Ee9iUL1BHvUK4", // Optional
  },
};

apiTriggerPowerAutomate(options);
```

One more change is needed to also send the SalesOrder number and the comment to the Power Automate Endpoint. For that, we need to add the following to the options object.

```js
data: {
        "messageID": Number(data.SalesOrder),
        "messageText": oTextAreaComments.getValue()
    }
```

The complete API call should look similar to this.

```js
var options = {
    parameters: {
        "api-version": "2016-06-01", // Optional 
        "sp": "%2Ftriggers%2Fmanual%2Frun", // Optional 
        "sv": "1.0", // Optional 
        "sig": "_UO6mNVAhQwYaFEdWBVsSZq_bolYF8Ee9iUL1BHvUK4" // Optional 
    },
    data: {
        "messageID": Number(data.SalesOrder),
        "messageText": oTextAreaComments.getValue()
    }
};

apiTriggerPowerAutomate(options);
```


### Run the application and trigger a call to Power Automate

Press the `Activate` button and press the `Run` button on top of the screen to start the application

- Open a Sales Order from the list and press the `Trigger Approval` button
![Trigger Approval](images/neptune-app-trigger-approval.jpg)

## 🤝 Switch back to Power Automate
- Open up [Teams](https://teams.microsoft.com/) and navigate to the Channel you had specified. If everything worked fine, you should see an adaptive card.
  ![Power Automate](images/14-AdaptiveCardInTeams.png)


### Add Approve / Reject
- Now we need to add the functionality the Approve / Reject the incoming workflow. The Teams action is actually waiting for a response, so we can add a Condition that allows us to send an HTTP response back to the SAP system.

-  `Under the Post adaptive card and wait for a response` action, click on the `+` and `Add an action`
  ![Power Automate](images/15-AddActionCondition.png)

- Search for `Condition` and click on the action
  ![Power Automate](images/16-AddCondition.png)

- In the Condition action, click on `Choose a value` and select `fx`
  ![Power Automate](images/17-AddCondition.png)

- Enter the function below to take the response from the Teams action

```
body('Post_Adaptive_Card_and_wait_for_a_response')?['submitActionId']
```

![Power Automate](images/18-EnterCondition.png)

- Now enter the value that we are checking: `Approve`
  ![Power Automate](images/19-EnterApprove.png)

- In the `True branch` of the Condition flow, click on `+` and `Add an Action`
  ![Power Automate](images/20-AddActionHTTP.png)

- Search for `HTTP` and select the `HTTP action`
  ![Power Automate](images/21-HTTPAction.png)

- Enter the following values
  - URI: `https://neptune-academy.neptune-software.cloud/api/serverscript/dsagtechexchangepowerautomate/approve`
  - Method: `POST`
  - Body:  
    ```json
    {
      "SalesOrder": @{triggerBody()?['messageID']}
    }
    ```

  ![Power Automate](images/24-HTTP-Approve.jpg)

- For the Authentication select 'Authentication' under Advanced parameters and enter the following values
  - Authentication Type: `Basic`
  - Username: `dsag`
  - Password: `dsag24` (you can also use your own username/password for your Neptune user)
![Power Automate](images/25-HTTP-Authentication.jpg)


- Save the Power Automate Flow

- This is how the flow overall should look like
  ![Power Automate](images/23-PA-Flow.png)


## 🤝 Switch back to Neptune DXP

You can now go back to your Neptune application in the browser or start it again from the App Designer.

- Trigger another Sales Order Approval
![Trigger Approval](images/neptune-app-trigger-approval.jpg)

- Approve the Sales Order in Teams
![Teams Approval](images/teams-approval.jpg)

- Refresh the Sales Orders list and check if the Sales Order is approved
![Neptune Approval](images/neptune-salesorder-approved.jpg)


