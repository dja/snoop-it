# Monitoring: Network #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`httpMonitorGetList`**: Returns a list of all HTTP requests using `NSURLConnection`
  * **`httpMonitorGetListUpdate`**: Returns a list of all HTTP requests, starting at a specific ID
  * **`httpMonitorGetId`**: Returns detailed information about a single HTTP request and the corresponding response
  * **`httpMonitorDeleteAll`**: Removes all entries from the HTTP request/response list

---


## Method: httpMonitorGetList ##
Returns a list of all HTTP requests using `NSURLConnection`.

Note: This method does not return the body of an HTTP request. If detailed information about a single HTTP request (including its body) is required, those data can be retrieved using the `httpMonitorGetId` method.

### Request ###
  * **Method Name**: `httpMonitorGetList`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|from     | double           | Yes           | -           | Returns only HTTP requests after a specified date (UNIX Timestamp) |
|to       | double           | Yes           | -           |  Returns only HTTP requests up to a specified date (UNIX Timestamp) |

**Sample Request**:
```
<methodCall>
	<methodName>httpMonitorGetList</methodName>
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
The response returns an array of structs. Each struct represents an atomic HTTP request/response. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | Unique identifier of each entry |
|timestamp | double           | No            | Date and time of each entry (UNIX Timestamp)  |
|url      | string           | No            | Requested URL   |
|method   | string           | Yes           | HTTP request method |
|request  | boolean          | No            | `true` in the case of an HTTP request, `false` if the entry represents an HTTP response |
|hasBody  | boolean          | No            | `true`, if HTTP body data is available |
|mime     | string           | No            | MIME type of the HTTP request |
|responseMime | string           | Yes           | MIME type of the HTTP response |


**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>url</name>
					<value><string>http://data.flurry.com/aas.do</string></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>request</name>
					<value><boolean>1</boolean></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183677.356116</double></value>
				</member>
				<member>
					<name>method</name>
					<value><string>POST</string></value>
				</member>
				<member>
					<name>hasBody</name>
					<value><boolean>1</boolean></value>
				</member>
				<member>
					<name>mime</name>
					<value><string>text/plain</string></value>
				</member>
			</struct></value>
			
			
			<value><struct>
				<member>
					<name>id</name>
					<value><i4>2</i4></value>
				</member>
				<member>
					<name>request</name>
					<value><boolean>1</boolean></value>
				</member>
				<member>
					<name>url</name>
					<value><string>http://data.flurry.com/aas.do</string></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183677.747763</double></value>
				</member>
				<member>
					<name>hasBody</name>
					<value><boolean>0</boolean></value>
				</member>
				<member>
					<name>mime</name>
					<value><string>text/plain</string></value>
				</member>
				<member>
					<name>responseMime</name>
					<value><string>text/plain</string></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: httpMonitorGetListUpdate ##
Returns a list of all HTTP requests, starting at a specific ID. This method can be used to incrementally update the displayed keychain list within a user frontend.

### Request ###
  * **Method Name**: `httpMonitorGetListUpdate`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|lastId   | i4               | No            | -           | Returns only entries with `IDs` greater than `lastId` |

**Sample Request**:
```
<methodCall>
	<methodName>httpMonitorGetListUpdate</methodName>
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
Identically equal to **httpMonitorGetList**


---


## Method: httpMonitorGetId ##
Returns detailed information about a single HTTP request and the corresponding response. To retrieve also the body of an HTTP request and its response, the value of the parameter `includeBody` must be set to `true`.


### Request ###
  * **Method Name**: `httpMonitorGetId`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|id       | i4               | No            | -           | Identifier of the entry  |
|includeBody | boolean          | Yes           | TRUE        | If the body of an HTTP request/response should be returned |

**Sample Request**:
```
<methodCall>
	<methodName>
		httpMonitorGetId
	</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>id</name>
				<value><int>1</int></value>
			</member>
			<member>
				<name>includeBody</name>
				<value><boolean>1</boolean></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents an atomic HTTP request/response. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | Unique identifier of each entry  |
|timestamp | double           | No            | Date and time of each entry (UNIX Timestamp) |
|url      | string           | No            | Requested URL   |
|method   | string           | Yes           | HTTP request method |
|request  | boolean          | No            | `true` in the case of an HTTP request, `false` if the entry represents an HTTP response |
|hasBody  | boolean          | No            | `true`, if HTTP body data is available |
|body     | string           | depends on the request parameter `includeBody` | Body data of the HTTP request/response |
|mime     | string           | No            | MIME type of the body |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>url</name>
					<value><string>http://data.flurry.com/aas.do</string></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>request</name>
					<value><boolean>1</boolean></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183660.271192</double></value>
				</member>
				<member>
					<name>method</name>
					<value><string>POST</string></value>
				</member>
				<member>
					<name>hasBody</name>
					<value><boolean>0</boolean></value>
				</member>
				<member>
					<name>mime</name>
					<value><string>application/octet-stream</string></value>
				</member>
			</struct></value>
			<value><struct>
				<member>
					<name>url</name>
					<value><string>http://data.flurry.com/aas.do</string></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>2</i4></value>
				</member>
				<member>
					<name>request</name>
					<value><boolean>0</boolean></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183661.678072</double></value>
				</member>
				<member>
					<name>hasBody</name>
					<value><boolean>0</boolean></value>
				</member>
				<member>
					<name>mime</name>
					<value><string>text/plain</string></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: httpMonitorDeleteAll ##
Removes all entries from the HTTP request/response list. ID counter is not resetted.

### Request ###
  * **Method Name**: `httpMonitorDeleteAll`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>httpMonitorDeleteAll</methodName>
	<params></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully