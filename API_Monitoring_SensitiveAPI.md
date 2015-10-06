# Monitoring: Sensitive API #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`sensitiveAPIGetList`**: Returns a list of access to sensitive API used within the App
  * **`sensitiveAPIGetListUpdate`**: Returns an updated list of access to sensitive API, starting at a specific ID
  * **`sensitiveAPIDeleteAll`**: Removes all entries from the list


---


## Explanation of Response Types ##

### Response Field: `api` ###
| **Key** | **Description** |
|:--------|:----------------|
| 1       | Unique Device ID (UDID) |
| 2       | Wifi MAC Address |
| 3       | Addressbook _(via API)_  |
| 4       | Calendar _(via API)_ |
| 5       | Photos / Videos |
| 6       | Location        |
| 7       | Addressbook _(via direct file access)_ |
| 8       | Calendar _(via direct file access)_ |
| 9       | Audio recording |
| 10      | Camera          |
| 11      | General Pasteboard |
| 12      | Find Pasteboard |
| 13      | Custom Pasteboard |

---


## Method: sensitiveAPIGetList ##
Returns a list of access to sensitive API used within the App.

### Request ###
  * **Method Name**: `sensitiveAPIGetList`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|from     | double           | Yes           | -           | Returns only sensitive API accesses _after_ a specified date (UNIX Timestamp)  |
|to       | double           | Yes           | -           | 	Returns only sensitive API accesses _up to_ a specified date (UNIX Timestamp)  |

**Sample Request**:
```
<methodCall>
	<methodName>sensitiveAPIGetList</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>to</name>
				<value><double>1340183678.0</double></value>
			</member>
			<member>
				<name>from</name>
				<value><double>1340183677.0</double></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents a single access of a speicifc sensitive API method. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | -               | Unique identifier of each entry in the sensitive API list  |
|api      | i4               | No            | -               | Sensitive API method that was accessed _(see above)_ |
|timestamp | double           | No            | -               | Date and time of each entry (UNIX Timestamp)  |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>id</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>api</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>>1340183677.356116</double></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: sensitiveAPIGetListUpdate ##
Returns an updated list of access to sensitive API, starting at a specific ID. This method can be used to incrementally update the displayed sensitive API access list within a user frontend.

### Request ###
  * **Method Name**: `sensitiveAPIGetListUpdate`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|lastId   | i4               | No            | -           | Reponse contains only entries with `IDs` greater than `lastId` |

**Sample Request**:
```
<methodCall>
	<methodName>sensitiveAPIGetListUpdate</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>lastId</name>
				<value><int>16</int></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
Identically equal to **sensitiveAPIGetList**


---


## Method: sensitiveAPIDeleteAll ##
Removes all entries from the list. `ID` counter is not resetted.

### Request ###
  * **Method Name**: `sensitiveAPIDeleteAll`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>sensitiveAPIDeleteAll</methodName>
	<params></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
