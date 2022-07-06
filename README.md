# HubSpot Connectivity using Boomi

## Introduction
HubSpot’s Customer Relationship Management (CRM) platform has the tools required for marketing, sales, content management, and customer service.  It can store Customer, Engagements, Product, Deals, and various other details related to CRM operations and transactions.

The objective of this document is to show the HubSpot configurations which are required to get it connected with Boomi to synchronize records with various other applications.

## HubSpot Authentication Options
### Option-1: Fetch HubSpot API Key

**Step-1:** Go to Settings --> Integrations --> API Key and click “Show”.
![image](https://user-images.githubusercontent.com/12267939/177537404-a5f00b18-97c1-42be-97ff-81bc12e669c1.png)


**Step-2:** Use this API key in Request URL as Query Parameter.

![image](https://user-images.githubusercontent.com/12267939/177537421-afd0ca6b-2645-4a02-8c4a-34d34691bb8a.png)


### Option-2: Create Private App in HubSpot
**Step-1:** Go to Settings --> Integrations --> Private Apps and click “Create a private app”.

![image](https://user-images.githubusercontent.com/12267939/177537751-d1edc528-47c0-460a-a245-681c0cfec566.png)

**Step-2:** Fill Basic Info.

![image](https://user-images.githubusercontent.com/12267939/177537772-3bf4fcb7-ed4e-4301-9714-d13b3aa65cc0.png)

**Step-3:** Select CRM Scopes and click “Create app”.

![image](https://user-images.githubusercontent.com/12267939/177537841-182e78a1-3308-45f2-b1de-dc2766e0b68c.png)


**Step-4:** A pop-up window will now open. Click “Continue creating”.

![image](https://user-images.githubusercontent.com/12267939/177537861-8f2ab71c-d01e-451b-a725-5c2430933bb1.png)


**Step-5:** Click “Show token” and then “Copy” to save it in a secure location.

![image](https://user-images.githubusercontent.com/12267939/177537866-e3c1ba2c-7e06-499e-9e39-aa1119660fc0.png)


**Step-6:** Token can also be viewed by going through Settings --> Private Apps --> Ayan’s API (Private App created in Step-4).

![image](https://user-images.githubusercontent.com/12267939/177537876-435d14d4-b474-47dd-8461-46af634fa049.png)

![image](https://user-images.githubusercontent.com/12267939/177537923-ae40c57c-71d3-43c5-ba23-bf64f4846206.png)


**Step-7:** Use this token as “Bearer Token” in the Request Header.

![image](https://user-images.githubusercontent.com/12267939/177537947-f8a49a2a-4389-4b61-b84e-d7665b069ed4.png)


## Implement Pagination in Boomi to Fetch Records from HubSpot
Overall Boomi process to fetch records from HubSpot using Pagination, and send the records to NetSuite.

![image](https://user-images.githubusercontent.com/12267939/177538132-eede2124-0261-456d-804c-2703eaadc23c.png)


**Step-1:** Set Last Successful Run Date

![image](https://user-images.githubusercontent.com/12267939/177538158-0ade98bc-56e9-4168-a022-945053c30b90.png)


**Step-2:** Set JSON profile for subsequent Map shape.

![image](https://user-images.githubusercontent.com/12267939/177538187-7c542750-1f79-4ea3-b45f-b7c008a193b0.png)


**Step-3:** Convert normal date value to EPOCH date value. Please refer "Normal Date to EPOCH Date.js" file for the conversion code. This is required because HubSpot accepts only EPOCH date in Search query.

![image](https://user-images.githubusercontent.com/12267939/177538207-8a82997a-e89d-417e-8d16-922885a7abf0.png)


**Step-4:** Set Request JSON to search HubSpot records.

![image](https://user-images.githubusercontent.com/12267939/177538376-e764ac23-65ab-4f50-be9f-3807bbda59bf.png)

**Message:** Please refer "Message Shape JSON.txt" file.
  
**Example:** Please refer "Message Shape JSON Example.json" file. Default Limit is 10 and Maximum Limit is 100. “Properties” key is used to fetch only the required fields from HubSpot.

**Step-5:** Set HubSpot connection.

![image](https://user-images.githubusercontent.com/12267939/177538443-e3b3d3fd-97c8-43dc-a77c-a2fe747454e5.png)


**Step-6:** Set HubSpot operation.

![image](https://user-images.githubusercontent.com/12267939/177538472-ebfd5015-9cc9-4899-b3e3-6e937295ec6f.png)


**Step-7:** If value of “total” key is greater than 0, then records exist in HubSpot and proceed with next steps in Branch-1.
Below is a Sample Response.

![image](https://user-images.githubusercontent.com/12267939/177538501-61484cb3-077a-49be-b58e-bdb8c421dc5f.png)

![image](https://user-images.githubusercontent.com/12267939/177538533-d612ba17-5df8-46b6-8d61-b348e5aa84c1.png)


**Step-8:** If value of “after” key is not null, then more records need to be fetched and pagination is required to be carried out in this Boomi process.

![image](https://user-images.githubusercontent.com/12267939/177538552-9359d649-9e82-4b6c-af42-de650800b4f7.png)

![image](https://user-images.githubusercontent.com/12267939/177538583-68bed447-4afb-4152-8966-63e3573fc8ca.png)


**Step-9:** Set value of “after” key in Dynamic Process Property.

![image](https://user-images.githubusercontent.com/12267939/177538572-9062f295-feb6-412e-a3f2-d3d73e053e59.png)


**Step-10:** Return to “Set HubSpot Filter Groups” Message shape to form a loop of requests being made to HubSpot. This loop will break once value of “after” key is null.


# Implement UPSERT Operation
Below is the sample Postman screenshot. 

![image](https://user-images.githubusercontent.com/12267939/177538654-81fdf889-5b1c-4042-a900-0dc847124627.png)


**Sample URL:** https://api.hubapi.com/contacts/v1/contact/createOrUpdate/email/test@hubspot.com/?hapikey=hubspot_api_key_value

where, test@hubspot.com is the email-id which needs to UPSERT, and “createOrUpdate” value in URL is to implement UPSERT operation.
  
**Sample Request Payload:** Please refer "Sample Request Payload.json" file.

**Sample Response Payload:** Please refer "Sample Response Payload.json" file.

If “isNew” value is “true”, then record is created. If “isNew” value is “false”, then record is updated.
