**<u>Rate API Implementation</u>**

Now that we have the rate api published in the exchange, we can start fetching it in Anypoint studio and implement the required business logic for the api.

 Open Anypoint studio and create a new mule project.
 
![](images/2.1.png)



Right clink on the project explorer pane, and select mule project.

![](images/2.2.png)


The following window opens

![](images/2.3.png)



Ø Project Name - ***\*rate-api\****

Ø Runtime - ***\*Mule Server 4.3.0\****

Ø We can uncheck the ***\*Use default location\**** checkbox and choose to save the project anywhere on the disk.

Ø Select the tab - ***\*Download RAML from Design Center.\*******\*
\****Click the browse option next to Location box, which will open the window below. Select the ***\*Business Group\**** and ***\*Project name\**** as shown. Click ***\*OK\****.


![](images/2.4.png)

Anypoint studio will fetch the API Spec from exchange and we should see the below window on successful fetch.

![](images/2.5.png)


Keep the option ***\*Scaffold flows from these API specifications\**** checked.

Click ***\*Finish\****. We should now see the project loaded as shown below with the api flows scaffolded from the RAML. All flows collapsed are as shown below.

![](images/2.6.png)

When we explore the generated flows, we find the following routes.

\1. rate-api-main - The entry point for every request

\2. rate-api-console - A client used to execute HTTP requests to the endpoints

\3. Get \health

\4. Get \live

\5. Get \ready

\6. Get \rate\quoteId

\7. Post \rate\quoteId

\8. Delete \rate\quoteId


health, live and ready routes are used to verify the health and readiness off the service to accept and execute requests. If we execute a request against the endpoint now, it would return a static json response indicating that the api is up and running.

As part of this exercise, we will limit the implementation of these endpoints to the current state. Also , we will use MongoDB as our Database for implementing our use-case.

**Implementing the GET \rate\quoteId route**

The business logic for the get route is to fetch a document from a collection,

Document and Collection are the terms in a NoSql DB like MongoDB. Document is similar to a row/record and Collection is equivalent of a table in SQL based DBs.

At this point, the route flow looks like below. We have to now include logic to connect to MongoDB to fetch a document.

![](images/2.7.png)

***\*Step 1 (Optional) : Add logger component\****

To capture the incoming request and display a log, we can add a logger component as the first connector in the flow. The logger connector can be found on Mule Palette.

![](images/2.8.png)


We can also remove the first transform message connector which is redundant for this flow. Add the logging message to the logger connector as shown below.

 ![](images/2.9.png)

In the message attribute for logger, we can see the Dataweave expression to capture the incoming quoteId from the URL - ***\*attributes.uriParams.\*******\*'quoteId\****'

 For example, if the URL is GET http://localhost:8081/api/rate/123, and since we have defined our route as ***\*/rate/{quoteId}\****, 123 from the URL would be assigned to quoteId param and will be accessible through the expression  ***\*attributes.uriParams.\*******\*'quoteId\****'


 ***\*Step\**** ***\*2: Add a mongo connector to fetch a single document\****

 Now that we have access to the incoming request and the quoteId, we can add a mongo connector to fetch a document from mongo db. 

Mongo Module and connectors are not found in the mule palette by default. When we search for mongo in mule palette, no results will be returned.

 ![](images/2.10.png)



In order to add it, we need to fetch it from exchange. In Mule Palette, click ***\*“Search in Exchange”.\****

![](images/2.11.png)


Search for mongo and add the MongoDB Connector Module for Mule 4. Click Finish.

 ![](images/2.12.png)


![](images/2.13.png)

Now we should be able to see the MongoDB module in Mule Palette. 

![](images/2.14.png)

Drag and Drop ***\*Find documents\**** connector.

![](images/2.15.png)

We need to configure the ***\*Find documents\**** connector by adding necessary attributes to the connector properties. We will configure the below.


l Connector Configuration - Mongo connection attributes

l Query to execute against mongo

l Collection name

l Fields 



***\*Connector Configuration\****

 Click on the + icon next to Connector configuration. The below window will open.

Add the connection string for the MongoDB in the Connection string field.

![](images/2.16.png)

 

Also, add MongoDB driver as shown above. Confirm adding the library by clicking OK. 

![](images/2.17.png)

 Once added, click Test Connection to test if we can connect to Mongo. This would ensure our successful connection to Mongo instance specified in Connection string.

We should see the connection acknowledgment as below.

![](images/2.18.png)


We will now query our collection with a query like the one below. This is a query to fetch documents from collection matching the search criteria _id=5.

***\*db.<collection_name>.find( { _id: 5 } )\****

Refer - [***\*https://docs.mongodb.com/manual/reference/method/db.collection.find/\****](https://docs.mongodb.com/manual/reference/method/db.collection.find/)   

Since we need to construct the search criteria for the query dynamically from the incoming URI param from the request, we will need a Transform Message component to construct the query search criteria in json format.

 Add a Transform Message connector before the Find document connector and configure it as shown below.

![](images/2.19.png)

The constructed JSON is stored in payload. But since payload can mean different things in a flow, as a practice we can store the output of this connector in a variable.

Click the pencil icon which will bring up the window below. Add a variable and name it as required. This variable name will be used in the find documents connector.

![](images/2.20.png)
 

 We have named it getQuery and now getQuery variable will hold the value 

**{**

​	**"quoteId": <quoteId param value from the incoming req>**

**}**

 Now we will use this variable in the Query field of the find documents connector.

Add the variable in the ***\*Query\**** field of the connector config.

 After the all the required attributes are accounted for, the configuration will look like below.

![](images/2.21.png)

***\*Step 3:\**** ***\*Update the Transform Message connector to return the output\****

 The transform message in the flow is currently configured to output the sample json that we have specified in the API Spec RAML as an example. We will now update this to return the actual response from the executed mongo query.

 The response from the query execution is returned as payload. We will save it in variable named as required.

![](images/2.22.png)

Once the variable is created, we will refer this variable in the Transform message connector to be returned as the output.

![](images/2.23.png)

 Now the get flow should be ready for testing. Run the mule project and execute the get route with a client like Postman and see if we see the response as expected. Choose an existing quoteId from mongo collection and try to fetch the corresponding document.

The console should display the message “Deployed” once the project is ready to accept requests.

![](images/2.24.png)

Execute a get query from Postman as below. We should be able to the see the whole document from the collection returned for the queried Quote Id.

![](images/2.25.png)

The console will show the message that we have configured in the logger connector.

![](images/2.26.png)

We have successfully created a get flow which is working as expected.

Follow the same pattern and create a Post and Delete flow using the appropriate connectors from the mongo connector documentation link provided below.

https://docs.mulesoft.com/mongodb-connector/6.3/mongodb-connector-reference
