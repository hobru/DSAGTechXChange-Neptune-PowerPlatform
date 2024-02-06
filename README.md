# DSAGTechXChange - No-Code/Low-Code Challenge

Hands-on Session as part of the [DSAG TechXChange 2024](https://dsag.de/wp-content/uploads/2023/12/Programm_und_Agenda_DSAG-TechXchange.pdf)

## 🥅 Goal

In this session we want to show how easiy it is to combine the strength of Neptunes DXP platform to build UI5 apps in a low-code approach and combine this with a Power Automate flow to send approval notifications to Teams. 

As a result you will have a simple App, that allows you to trigger an Approval for a SAP Sales Order, then see the notification in Teams and approve it directly from there. 




## 🤝 Neptune DXP

Login to the Neptune Cockpit

https://neptune-academy.neptune-software.cloud/cockpit.html

- Username: dsagxx (for xx user your group number e.g. dsag1)
- Password: dsag24

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

This is the service we will use in our application

Return to the App Designer

- Drag the ODataSource from the tree to the Resource and rename it SalesOrderOData

![Connect OData](images/neptune-app-designer-odata.jpg)

- With the `SalesOrderData` service selected on the right of the screen select the oData source `SalesOrder` from the list

- Drag the RestAPI from the tree to Resource and rename it to TriggerPowerAutomate

![Connect RESTAPI](images/neptune-powerautomate-restapi.jpg)

### Test the app

Run the application again and check if the Sales Orders are displayed in the list and you can click a item from the list to see the details

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

- Select the Channel and Teams where the Adaptive Card should be posted
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

Here we will add a new API for the Power Automate Trigger and this API can be called from the Neptune Application.


- Click on the `Add` button to create a new API
![new API](images/neptune-add-api.jpg)

- Fill in a unique name e.g. `DSAGTechExchangePowerAutomateTrigger_xx` where `xx` is your group number and copy only the first part of the URL until /path from Power Automate as `Endpoint`
eg. `https://prod-150.westeurope.logic.azure.com:443/workflows/9a92351242184f7d9fc6f322cddf9df3/triggers/manual/paths`
![API Designer Add](images/neptune-api-designer.jpg)

- Enable the checkbox for `Enable Proxy` and `Use in App Designer & App Editor`
![API Designer checkbox](images/neptune-api-designer2.jpg)

- Go to the `Operations` tab and press the `+` button and enter the following values
  - Path: `/invoke`
  - Method: `POST`

- Go to the Request tab and enter the following parameters
  - api-version: `2016-06-01`
  - sp: `%2Ftriggers%2Fmanual%2Frun`
  - sv: `1.0`
  - sig: `_UO6mNVAhQwYaFEdWBVsSZq_bolYF8Ee9iUL1BHvUK4`
  
  ![API Designer parameters](images/neptune-api-designer3.jpg)


- Finally press `Save` to store the API Endpoint

### App Designer

Now we can use this API endpoint in our application. Open the App Designer again https://neptune-academy.neptune-software.cloud/appdesigner.html

- In the Tree library search for `RestAPI` and drag it under resources 
![App Designer RestAPI](images/neptune-appdesigner-restapi.jpg)

- Rename the `RestAPI` to `TriggerPowerAutomate`

Now we will implement some code behind the Trigger Button.

Search for the `ButtonTrigger` in the UI tree and select it

![Trigger Button](images/neptune-trigger-button.jpg)

On the right side of the screen in the panel click the button next to the press function

![Trigger press](images/neptune-trigger-button-press.jpg)

Add the place where the `TODO` comment is you can add the API call by using the code snippets functionality.

- Right-click in the Javascript file at the place you want to put the code
- Collapse the API option and select `TriggerPowerAutomate` and press `Copy`


The code similar to below will be copied. The values might slight differ because the parameters from each users are different because of the different Power Automate API Endpoints
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

### Run the application and trigger a call to Power Automate

Press the `Run` button on top of the screen to start the application

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


