# Other: Availability Check #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`ping`**: Verify if the XML-RPC web service is available


---


## Method: `ping` ##
This method can be used, to verify the availability of the XML-RPC web service.

### Request ###
  * **Method Name**: ping
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>ping</methodName>
	<params></params>
</methodCall>
```

### Response ###
The reponse contains an array. The first item is a simple **PONG** string. The second item is a session identifier. This identifier can be used to determine if the current data is still valid. If the session identifier changes all data has to be reloaded.

**Sample Response (excerpt)**:
```
<methodResponse>
	<params>
		<param>
			<value><array><data>
				<value><string>PONG</string></value>
				<value><string>52294CC2-CEBF-4930-954A-E14717C20994</string></value>
			</data></array></value>
		</param>
	</params>
</methodResponse>
```