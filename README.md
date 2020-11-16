# RATE API - API Spec

## Objective

It is a quick reference guide to understand usages of key components & capabilities in Mule that developer/analyst may require during interface design and build phase. It also contains details and references to help kick start initial set-up for development, deployment and test of Mule interfaces as well as references to standard recommendations.

## Use Case:
This example demonstrates creating a Rate API for NEION App Use Case, which is used to collect and update coverages information, invokes underwriting before rate and returns response accordingly. Using this API, along with the above mentioned features we will be able to secure user to only fetch/update coverages they are part of. In order to process these requests, the use case uses Mule to invoke HTTP requests to system API's and other sources to fetch the details

At the end of this course, students should be able to:
•	Describe the benefits of application networks and API-led connectivity.
•	Use Anypoint Exchange as a central repository for the discovery and reuse of assets. 
•	Use Flow Designer to build integration apps that consume APIs from Exchange and transform data.
•	Use API Designer to define APIs with RAML and make them discoverable by adding them to Exchange. 
•	Use Anypoint Studio to build APIs that connect to databases and transform data with Data Weave. 
•	Deploy API implementations to Cloud Hub. 
•	Use API Manager to create and deploy API proxies that govern access to APIs.


## Prerequisties

### `Knowledge` ###

1.	A Basic understanding of data formats such as XML and JSON.
2.	A basic understanding of typical integration technologies such as HTTP, REST and SOAP. 

### `Anypoint Studio Installation Procedure` ###

3.	Check hardware and software requirements
4.	Check system compatibility with platform software
5.	Download Anypoint Studio by following the document , then launch Mule
    https://training.mulesoft.com/oltpublish/cmsres/downloads/APStart4_setup.pdf 
    
### `Training` ###

1.	Each participant should have completed the Anypoint Platform Development: Fundamentals (Mule 4)     
    https://training.mulesoft.com/course/development-fundamentals-mule4


## Anypoint web platform setup

### `Create an account` ###

https://docs.mulesoft.com/anypoint-platform-administration/creating-an-account

##  RATE API Spec Design

The code is located in the file `src/main/mule/business-logic.xml` in the project.

*Figure 1: The content of `business-logic.xml`*

```xml
<mule xmlns:http="http://www.mulesoft.org/schema/mule/http" xmlns="http://www.mulesoft.org/schema/mule/core" ... >

	<http:listener-config name="HTTP_Listener_config" doc:name="HTTP Listener config" >
		<http:listener-connection host="0.0.0.0" port="${http.port}" />
	</http:listener-config>

	<configuration-properties file="mule-artifact.properties" doc:name="Configuration properties" />

	<flow name="mainFlow" >
		<http:listener config-ref="HTTP_Listener_config" path="/" doc:name="HTTP Listener" allowedMethods="GET"/>
		<set-payload value="#[attributes.queryParams.url_key]" doc:name="Set query param 'url_key' to payload" />
		<flow-ref name="secondaryFlow" doc:name="secondaryFlow" />
		<choice doc:name="Choice" >
			<when expression="#[vars.flowValue == 'flowValue_1']" >
				<set-payload value="#['responsePayload_1']" doc:name="Set Response Payload" />
			</when>
			<otherwise >
				<set-payload value="#['responsePayload_2']" doc:name="Set Response Payload" />
			</otherwise>
		</choice>
	</flow>

	<flow name="secondaryFlow" >
		<choice doc:name="Choice" >
			<when expression="payload == 'payload_1'" >
				<flow-ref name="firstSubFlow" doc:name="firstSubFlow" />
			</when>
			<otherwise >
				<flow-ref name="secondSubFlow" doc:name="secondSubFlow" />
			</otherwise>
		</choice>
	</flow>

	<sub-flow name="firstSubFlow" >
		<set-variable variableName="flowValue" value="flowValue_1" doc:name="Set Variable"  />
	</sub-flow>

	<sub-flow name="secondSubFlow" >
		<set-variable variableName="flowValue" value="flowValue_2" doc:name="Set Variable" />
	</sub-flow>
</mule>
```
## Conclusion

In this tutorial, we've seen:

* How to create RATE API Spec  etc.
