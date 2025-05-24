---
title: "Power Automate: Filter Emails Which Have No Category"
date: 2025-05-24
draft: false
---

### In this Article I want to show you how to filter no category email in outlook via Power Automate.

In current Outlook 365 connector, there is no option to filter out emails with/without categories.

When I tried to build the flow, I thought I could use `Get emails (V3)`. After I tried I realised that there is no info in the body, but it doesn't have the categories information, and the `most important thing is it may consume more API if I use it first`.

Below is an example of one message structure from `Get emails (V3)` output body value.

```json
            {
                "id": "value",
                "receivedDateTime": "",
                "hasAttachments": false,
                "internetMessageId": "",
                "subject": "",
                "bodyPreview": "Hi...",
                "importance": "normal",
                "conversationId": "",
                "isRead": false,
                "isHtml": true,
                "body": "html body",
                "from": "",
                "toRecipients": "",
                "attachments": []
            },
```

After check each actions, one option can be used is `Send an HTTP request` within Outlook 365 connector.combine with ***Get emails (V3)***

### Use `Send an HTTP request`

This action construct a Microsoft Graph REST API request to invoke. These segments are supported: 

1. 1st segment: /me, /users/ 
2. 2nd segment: messages, mailFolders, events, calendar, calendars, outlook, inferenceClassification

https://graph.microsoft.com/v1.0/me/messages will be the way to go. But I only want to check my inbox and unread messages.

https://graph.microsoft.com/v1.0/me/mailFolders/Inbox/messages/{messageID} will be perfect for me.

For demonstrate purpose，pseudocode as below：

 ```pseudocode
 optional, get emails V3
 Send an request to query inbox message and apply to unread message.
 Filter if any uncategorized message.
 For each message found, assign category.
 ```

### What won't work:

1. ##### Use Odata filter
   
   ```json
   filter=categories eq null //not work
   filter=categories in //not work
   filter=categories/any(c:c eq '')//not work
   filter=categories/any(c:c eq null)//not work
   filter=categories/any(c:c eq 'null')//not work
   filter=categories/any(c:c eq '')//not work
   filter=not categories/any() //only work with /me/messages without messgeid varible of course, if purely filter message box only it will work.
   ```
   
   
   
2. ##### Use condition
   
   ```json
   equals(item()?['categories'], null) //not work.
   equals(length(item()?['categories']), 0)) //nope
   ```

### Does WORK.

```json
empty(item()?['categories'])
//Returns true if the object, array, or string is empty.
```

Yes, this is the right one for this scenario.

### Flow overview.

![](/flowOverview.png)

### Flow break down:

1. ##### Initialize variable for OData query
   
   ```json
   {
     "type": "InitializeVariable",
     "inputs": {
       "variables": [
         {
           "name": "MyQuery",
           "type": "string",
           "value": "filter=isRead eq false&$select=categories&$top=10"
         }
       ]
     },
     "runAfter": {}
   }
   ```
   
   
   
2. ##### Send an HTTP request
   
   ```json
   {
     "type": "OpenApiConnection",
     "inputs": {
       "parameters": {
         "Uri": "https://graph.microsoft.com/v1.0/me/mailFolders/Inbox/messages?$@{variables('MyQuery')}",
         "Method": "GET",
         "ContentType": "application/json"
       },
       "host": {
         "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365",
         "connection": "shared_office365",
         "operationId": "HttpRequest"
       }
     },
     "runAfter": {
       "Initialize_variable_for_OData_query": [
         "Succeeded"
       ]
     }
   }
   ```
   
   
   
3. ##### Filter array only uncategorized message
   
   ```json
   {
     "type": "Query",
     "inputs": {
       "from": "@body('Send_an_HTTP_request')?['value']",
       "where": "@empty(item()?['categories'])"
     },
     "runAfter": {
       "Send_an_HTTP_request": [
         "Succeeded"
       ]
     }
   }
   ```
   
   
   
4. ##### For each uncategorized message
   
   ```json
   {
     "type": "Foreach",
     "foreach": "@outputs('Filter_array_only_uncategorized_message')['body']",
     "actions": {
       "Assigns_an_Outlook_category": {
         "type": "OpenApiConnection",
         "inputs": {
           "parameters": {
             "messageId": "@items('For_each_uncategorized_message')?['id']",
             "category": "FollowUP" //Your category name.
           },
           "host": {
             "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365",
             "connection": "shared_office365",
             "operationId": "AssignCategory"
           }
         }
       }
     },
     "runAfter": {
       "Filter_array_only_uncategorized_message": [
         "Succeeded"
       ]
     }
   }
   ```
   
   
   
5. ##### Assigns an Outlook category
   
   ```json
   {
     "type": "OpenApiConnection",
     "inputs": {
       "parameters": {
         "messageId": "@items('For_each_uncategorized_message')?['id']",
         "category": "FollowUP"
       },
       "host": {
         "apiId": "/providers/Microsoft.PowerApps/apis/shared_office365",
         "connection": "shared_office365",
         "operationId": "AssignCategory"
       }
     }
   }
   ```

### Compare another one I created before which consume more API.

![FilterNonCategoryEmail](/FilterNonCategoryEmail.gif)

**Note:** The examples shown use only generic and safe identifiers. Always review your own flow exports for sensitive info before sharing publicly.

Enjoy.





