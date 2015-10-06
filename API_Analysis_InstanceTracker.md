# Analysis / Manipulation: Instance Tracker #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`instanceTrackerGetAll`**: Returns a list of all available instances of App-defined classes
  * **`instanceTrackerGetForClass`**: Returns a list of all instances of a specific class
  * **`instanceTrackerInvokeSelector`**: Invokes a specific method _(selector)_
  * **`instanceTrackerAllocInit`**: Creates a new instance of a class and invokes its constructor

---


## Method: `instanceTrackerGetAll` ##
Returns a list of all available instances. Only instantiation of App-defined classes is tracked.

### Request ###
  * **Method Name**: instanceTrackerGetAll
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>instanceTrackerGetAll</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents a single class. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|class    | string           | No            | Class Name      |
|instance | string           | No            | Identifier of its instance (== memory address) |

**Sample Response (Excerpt)**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>class</name>
					<value><string>TTButtonContent</string></value>
				</member>
				<member>
					<name>instance</name>
					<value><string>0x5a90610</string></value>
				</member>
			</struct></value>
			
			...
			
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: `instanceTrackerGetForClass` ##
Returns a list of all instances of a specific class.

### Request ###
  * **Method Name**: instanceTrackerGetForClass
  * **Parameter**: A struct conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|class    | string           | No            | -           | Name of the class, whose concrete instances should be returned |

**Sample Request**:
```
<methodCall>
	<methodName>instanceTrackerGetForClass</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>class</name>
				<value><string>TTButtonContent</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of strings. Each entry displays the identifier of a concrete instance (== its memory address).

Format: Array of strings

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><string>0x5bba660</string></value>
			<value><string>0x5bbd560</string></value>
			<value><string>0x5bbdba0</string></value>
			<value><string>0x5bbe040</string></value>
			<value><string>0x342b50</string></value>
			<value><string>0x3451d0</string></value>
			<value><string>0x5bb9060</string></value>
			
			...
			
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: `instanceTrackerInvokeSelector` ##
Invokes a specific method of an arbitary Objective-C class. Both, invocation of class-methods as well as instance-methods is supported.

### Request ###
  * **Method Name**: instanceTrackerInvokeSelector
  * **Parameter**: A struct conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|class    | string           | No            | -           | Class name      |
|selector | string           | No            | -           | Selector name (== method.name from _ClassDump_) |
|instance | string           | Yes           | -           | Identifier of the instance, which should invoke the method. Can be ommitted in class-method invocation.  |
|args     | Array            | Yes           | -           | Values for a method's arguments. Arguments must match a method's signature (types and order). Snoop-it tries to automatically convert the supplied data to the correct data types (like e.g. implicit cast _int_ to _NSNumber_ etc.)|
|onMainThread | boolean          | Yes           | true        | Determines, if the method should be invoked in the main thread. |


**Sample Request**:
```
<methodCall>
	<methodName>instanceTrackerInvokeSelector</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>class</name>
				<value><string>SimpleTest</string></value>
			</member>
			<member>
				<name>selector</name>
				<value><string>helloSnoopi:</string></value>
			</member>
			<member>
				<name>args</name>
				<value><array><data>
					<value><string>Markus</string></value>
				</data></array></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an **array** of **structs**.
Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|type     | string           | No            | Returns the data type of the method's reponse. |
|value    | string           | Yes           | Returns the method's return value, if available. |
|instance | string           | Yes           | If a method creates a new instance, the address of the newly created instance is returned.  |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>instance</name>
					<value><string>0x4ae40e0</name></value>
				</member>
				<member>
					<name>type</name>
					<value><string>NSString</string></value>
				</member>
				<member>
					<name>value</name>
					<value><string>Hello! How are you today?</string></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: `instanceTrackerAllocInit` ##
Creates a new instance of a class and invokes its constructor.

### Request ###
  * **Method Name**: instanceTrackerAllocInit
  * **Parameter**: A struct conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|class    | string           | No            | -           | Class name      |
|selector | string           | No            | -           | Constructor name (related `initMethod` entry from ClassDump) |
|args     | Array            | Yes           | -           | Values for a method's arguments. Arguments must match a method's signature (types and order). Snoop-it tries to automatically convert the supplied data to the correct data types (like e.g. implicit cast _int_ to _NSNumber_ etc.)|
|onMainThread | boolean          | Yes           | true        | Determines, if the method should be invoked in the main thread. |


**Sample Request**:
```
<methodCall>
	<methodName>instanceTrackerAllocInit</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>class</name>
				<value><string>SimpleTest</string></value>
			</member>
			<member>
				<name>selector</name>
				<value><string>initWithName:</string></value>
			</member>
			<member>
				<name>args</name>
				<value><array><data>
					<value><string>Markus</string></value>
				</data></array></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

Identically equal to **`instanceTrackerInvokeSelector`**