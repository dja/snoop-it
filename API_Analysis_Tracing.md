# Analysis / Manipulation: Tracing #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`tracingGet`**: Returns the current tracing configuration
  * **`tracingSet`**: Modifies the current tracing configuration
  * **`tracingGetCache`**: Returns recent entries from the tracing cache
  * **`tracingGetLogFileInfo`**: Returns a list of filenames of all available tracing log-files
  * **`tracingDeleteLogFile`**: Removes a tracing log-file from the device


---



## Explanation of Configuration Options ##

### Request/Response Field: `mode` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Tracing disabled |
| 1       | Tracing enabled |


### Request/Response Field: `filter` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | No Filter (apart from `alloc`, `copy`, `retain`, `release`, `dealloc`, `autorelease`) |
| 1       | Display App-defined classes only. |
| 2       | Display App-defined classes, in addition filter less important runtime classes as well (e.g. internal invocations of `UIView`, etc.)  |
| 3       | Display App-defined classes which are even not inherited. |



---


## Method: tracingGet ##
Returns the current tracing configuration.

### Request ###
  * **Method Name**: tracingGet
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>tracingGet</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of a struct. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|mode     | i4               | No            | Current tracing mode _(see above)_ |
|cacheSize | i4               | No            | Number of entries that should be buffered in the tracing cache |
|filter   | i4               | No            | Current tracing filter _(see above)_ |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>mode</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>cacheSize</name>
					<value><i4>250</i4></value>
				</member>
				<member>
					<name>filter</name>
					<value><i4>1</i4></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: tracingSet ##
Modifies the current tracing configuration.

### Request ###
  * **Method Name**: tracingSet
  * **Parameter**: A struct conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|mode     | i4               | Yes           | -           | Sets the current tracing mode _(see above)_ |
|cacheSize | i4               | Yes           | -           | Modifies the size of the tracing cache |
|filter   | i4               | Yes           | -           | Sets a new tracing filter _(see above)_ |

**Sample Request**:
```
<methodCall>
	<methodName>tracingSet</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>mode</name>
				<value><i4>0</i4></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the configuration was changed successfully


---


## Method: tracingGetCache ##
Returns the last `n` entries of the tracing cache. `n` is determined by `cacheSize` _(see configuration)_.

### Request ###

  * **Method Name**: tracingGetCache
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>tracingGetCache</methodName>
	<params></params>
</methodCall>
```

### Response ###

The response returns an array of strings. Each entry represents a single entry (a method invocation) in the tracing-log.

**Sample Response**:
```
<methodCall>
	<methodName>tracingSet</methodName>
	<params>
		<param><value><array><data>
			<value><string>Wed Oct 10 17:14:35 2012 (Thread 0): - [NetworkAvailability isReachable]</string></value>
			<value><string>Wed Oct 10 17:14:35 2012 (Thread 0): + [Reachability reachabilityWithHostName:], args: <__NSCFConstantString 0x20316c: www.google.com></string></value>
			<value><string>Wed Oct 10 17:14:35 2012 (Thread 1): - [Reachability class]</string></value>
			
			...
			
		</data></array></value></param>
	</params>
</methodCall>
```


---


> ## Method: tracingGetLogFileInfo ##
Returns a list of filenames of all available tracing log-files.

### Request ###
  * **Method Name**: tracingGetLogFileInfo
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>tracingGetLogFileInfo</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|currentLogFile | struct           | No            | Returns a single `LogFileInfo` struct _(see below)_ |
|oldLogFiles | array            | No            | Array of `LogFileInfo` structs _(see below)_ |


**Response Field: `LogFileInfo`**:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|logFileName | string           | No            | Filename        |
|logFileSize | i4               | No            | File size in Byte |
|logFileURL | string           | No            | Relative URL to download the log-file (see [Filesystem Access](API_Monitoring_Filesystem.md))|


**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>currentLogFile</name>
					<value><struct></struct></value>
				</member>
				<member>
					<name>oldLogFiles</name>
					<value>
						<array><data><value><struct>
							<member>
								<name>logFileName</name>
								<value><string>trace_2012-10-10_10-20-49.log</string></value>
							</member>
							<member>
								<name>logFileSize</name>
								<value><i4>544324</i4></value>
							</member>
							<member>
								<name>logFileURL</name>
								<value><string>/trace-logs/trace_2012-10-10_10-20-49.log</string></value>
							</member>
						</struct></value></data></array>
					</value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: tracingDeleteLogFile ##
Removes a tracing log-file from the device.

### Request ###
  * **Method Name**: tracingDeleteLogFile
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|logFileName | string           | No            | -           | Name of the log-file to be removed |


**Sample Request**:
```
<methodCall>
	<methodName>tracingDeleteLogFile</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>logFileName</name>
				<value><string>trace_2012-10-10_10-20-49.log</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully