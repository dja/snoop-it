# Analysis / Manipulation: Location Spoofer #

## Overview ##

Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`locationSpooferGet`**: Returns the current location spoofing configuration and status
  * **`locationSpooferSet`**: Activates spoofing of a specific location
  * **`locationSpooferReset`**: Disables location spoofing


---


## Method: locationSpooferGet ##
Returns the current location spoofing configuration and status.

### Request ###
  * **Method Name**: locationSpooferGet
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>locationSpooferGet</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of a single struct. This struct returns the current location spoofing configuration in the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|isLocationSpoofingActive | boolean          | No            | States, if location spoofing is currently activated |
|latitude | double           | Yes           | Returns the geographic _latitude_ coordinate |
|longitude | double           | Yes           | Returns the geographic _longitude_ coordinate |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>isLocationSpoofingActive</name>
					<value><boolean>0</boolean></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: locationSpooferSet ##
Activates location spoofing to a given location.

### Request ###
  * **Method Name**: locationSpooferSet
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|latitude | double           | No            | -           | Geographic _latitude_ coordinate of the fake location |
|longitude | double           | No            | -           | Geographic _longitude_ coordinate of the fake location|


**Sample Request**:
```
<methodCall>
	<methodName>locationSpooferSet</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>latitude</name>
				<value><double>40.0</double></value>
			</member>
			<member>
				<name>longitude</name>
				<value><double>60.0</double></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly


---


## Method: locationSpooferReset ##
Resets the geographic coordinate settings and disables location spoofing.

### Request ###
  * **Method Name**: locationSpooferReset
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>locationSpooferReset</methodName>
	<params></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly


