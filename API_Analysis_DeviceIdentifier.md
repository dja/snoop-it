## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`hwIdentifierGet`**: Returns the current values of all hardware identifiers
  * **`hwIdentifierSet`**: Modifies the value of a specific hardware identifier
  * **`hwIdentifierReset`**: Resets a hardware identifiers to its original value


---



## Explanation of Configuration Options ##

### Request/Response Field: `deviceModel` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Unknown         |
| 1       | iPhone          |
| 2       | iPod touch      |
| 3       | iPad            |


---


## Method: hwIdentifierGet ##
Returns the current values of all hardware identifiers.

### Request ###
  * **Method Name**: hwIdentifierGet
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>hwIdentifierGet</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of a single struct. This struct contains the current values of all hardware identifiers.
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|realUDID | string           | No            | Original Unique Device ID (UDID) |
|realWifiMac | base64           | No            | Original Wifi Mac Address |
|realDeviceModel | i4               | No            | Original Device Model _(see above)_ |
|udid     | string           | Yes           | Faked UDID      |
|wifiMac  | base64           | Yes           |  Faked Wifi Mac Address |
|deviceModel | i4               | Yes           |  Faked Device Model _(see above)_ |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>realDeviceModel</name>
					<value><i4>2</i4></value>
				</member>
				<member>
					<name>realUDID</name>
					<value><string>ffffffffffffffffffffffffffffffffffffffff</string></value>
				</member>
				<member>
					<name>realWifiMac</name>
					<value><base64>GOf0MjZb</base64></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: hwIdentifierSet ##
Modifies the value of a specific hardware identifier.

### Request ###
  * **Method Name**: hwIdentifierSet
  * **Parameter**: A struct conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|udid     | string           | Yes           | -           | If specified, this value will be used as "new" UDID  |
|wifiMac  | base64/string    | Yes           | -           | If specified, this value will be used as "new" Wifi Mac Address. Must be declared as base64 or hexadecimal string. |
|deviceModel | i4               | Yes           | -           | If specified, this value will be used as "new" device model _(see above)_ |


**Sample Request**:
```
<methodCall>
	<methodName>hwIdentifierSet</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>udid</name>
				<value><string>eeeeeeeeeeeeefffffffffeeeeeeefefefefefef</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly


---


## Method: hwIdentifierReset ##
Resets a hardware identifiers to its original value.

### Request ###
  * **Method Name**: hwIdentifierReset
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
| reset   | array (of strings, see table below) | Yes           | -           | If one or more hardware identifier is specified, its value is reset. If no specific hardware identifier is specified, the value of all identifiers are reset to their original values. |

Available elements to be used within the reset array:
| **Key** | **Description** |
|:--------|:----------------|
| udid    | Reset the UDID value |
| wifiMac | Reset the Wifi Mac Address value |
| deviceModel | Reset the device model |


**Sample Request**:
```
<methodCall>
	<methodName>hwIdentifierReset</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>reset</name>
				<value>
					<array><data>
						<value><string>udid</string></value>
						<value><string>wifiMac</string></value>
					</data></array>
				</value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly


