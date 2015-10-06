# Analysis / Manipulation: Classes and Methods #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **classDumpGetClassTree**: Returns a tree of all available class definitions
  * **classDumpGetDirectSubclasses**: Returns a list of all subclasses of a specific class
  * **classDumpGetClass**: Returns detailed information about a specific class (e.g. its fields, methods etc.)
  * **classDumpGetProtocol**: Returns detailed information about a specific protocol definition
  * **classDumpSearch**: Searches the class tree for specific classes, methods, instances and instance variables

---


## Explanation of Request/Response Types ##

### Request/Response Field: `type` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Class is defined within the app |
| 1       | Class is a framework class |
| 2       | Class is a Snoop-it class |
| 3       | Class is derived from any other source (e.g. MobileSubstrate Tweaks) |


---


## Method: classDumpGetClassTree ##

Returns a tree of all available class definitions.

Note: Framework classes, that are not inherited by app defined classes, are filtered out for reasons of clarity.

### Request ###
  * **Method Name**: `classDumpGetClassTree`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>classDumpGetClassTree</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents a single class definition. Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Class name      |
|type     | i4               | No            | Type or source of class _(see above)_ |
|subClasses | Array (of structs) | No            | List of subclasses |

**Sample Response (Excerpt)**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>name</name>
					<value><string>NSObject</string></value>
				</member>
				<member>
					<name>type</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>subClasses</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>SnoopiXMLRPCResponseEncoder</string></value>
								</member>
								<member>
									<name>type</name>
									<value><i4>3</i4></value>
								</member>
								<member>
									<name>subClasses</name>
									<value>
										<array><data></data></array>
									</value>
								</member>
							</struct></value>
						</data></array>
					</value>
				</member>
			</struct></value>
			
			...
			
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: classDumpGetDirectSubclasses ##
Returns a list of direct subclasses of a specific class. Can be used to dynamically reload parts of the class tree, e.g. within a graphical user interface.

### Request ###
  * **Method Name**: `classDumpGetDirectSubclasses`,
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|rootClass | string           | No            | -           | Name of the class, whose subclasses should be enumerated (in order to display all subclasses from the very beginning of the tree, the keyword "ROOT" should be used) |

**Sample Request**:
```
<methodCall>
	<methodName>classDumpGetDirectSubclasses</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>rootClass</name>
				<value><string>UIViewController</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents a single subclass.

Format: Identically equal to **`classDumpGetClassTree`**

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>super</name>
					<value><string>UIViewController</name></value>
				</member>
				<member>
					<name>name</name>
					<value><string>EditorController</string></value>
				</member>
				<member>
					<name>type</name>
					<value><i4>0</i4></value>
				</member>
			</struct></value>
			<value><struct>
				<member>
					<name>super</name>
					<value><string>UIViewController</name></value>
				</member>
				<member>
					<name>name</name>
					<value><string>DetailViewController</string></value>
				</member>
				<member>
					<name>type</name>
					<value><i4>0</i4></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: classDumpGetClass ##
Returns detailed information about a specific class. This includes all registered class or instance methods, member variables, properties and protocols.

### Request ###
  * **Method Name**: `classDumpGetClass`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|class    | string           | No            | -           | Class name      |

**Sample Request**:
```
<methodCall>
	<methodName>classDumpGetClass</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>class</name>
				<value><string>MySampleClass</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs.
Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Class name      |
|super    | string           | No            | Name of its super class. The keyword "ROOT" is used, if a class has no super class. |
|type     | i4               | No            | Type or source of class _(see above)_ |
|protocols | Array (of strings) | No            | Names of all protocols that are implemented by this class |
|staticMethods | Array (of `method` structs) | No            | Class methods   |
|instanceMethods | Array (of `method` structs, _see below_) | No            | Instance methods |
|instanceVariables | Array (of `ivar` structs, _see below_) | No            | Instance variables |
|properties | Array (of `property` structs, _see below_) | No            | Class properties |
|imageName | String           | No            | Source of the class (e.g. binary or library) |
|initMethods | Array (of strings) | Yes           | List of all `init` methods. These methods can be used to manually create a new instance of a class using Snoop-it. |


Struct: **`method`**
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Method / selector name |
|type     | string           | No            | Return type     |
|argTypes | Array (of strings) | Yes           | Type of arguments to this method |

Struct: **`ivar`**
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Name of the instance variable |
|type     | string           | No            | Type of the instance variable |

Struct: **`property`**
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Name of the property |
|type     | string           | No            | Type of the property |
|ivar     | string           | No            | Instance variable that contains all property data |
|attr     | Array (von strings) | Yes           | Attributes of the property (e.g. `nonatomic`, `retain`, ...) |


**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>super</name>
					<value><string>NSObject</name></value>
				</member>
				<member>
					<name>name</name>
					<value><string>MySampleClass</string></value>
				</member>
				<member>
					<name>type</name>
					<value><i4>0</i4></value>
				</member>
				<member>
					<name>imageName</name>
					<value><string>/var/mobile/Applications/--------/XYZ.app/XYZ</string></value>
				</member>
				<member>
					<name>protocols</name>
					<value>
						<array><data></data></array>
					</value>
				</member>
				<member>
					<name>instanceVariables</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>_isNice</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>char</string></value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>text</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>NSString*</string></value>
								</member>
							</struct></value>
						</data></array>
					</value>
				</member>
				<member>
					<name>staticMethods</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>staticMethodWithIntReturn</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>int</string></value>
								</member>
							</struct></value>
						</data></array>
					</value>
				</member>
				<member>
					<name>instanceMethods</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>printText</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>void</string></value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>printSpecialText:reversedOrder:</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>void</string></value>
								</member>
								<member>
									<name>argTypes</name>
									<value>
										<array><data>
											<value><string>char</string></value>
											<value><string>char</string></value>
										</data></array>
									</value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>isNice</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>char</string></value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>setText:</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>void</string></value>
								</member>
								<member>
									<name>argTypes</name>
									<value>
										<array><data>
											<value><string>id</string></value>
										</data></array>
									</value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>text</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>id</string></value>
								</member>
							</struct></value>
						</data></array>
					</value>
				</member>
				<member>
					<name>properties</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>isNice</string></value>
								</member>
								<member>
									<name>ivar</name>
									<value><string>_isNice</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>int</string></value>
								</member>
								<member>
									<name>attr</name>
									<value>
										<array><data>
											<value><string>readonly</string></value>
										</data></array>
									</value>
								</member>
							</struct></value>
							<value><struct>
								<member>
									<name>name</name>
									<value><string>text</string></value>
								</member>
								<member>
									<name>ivar</name>
									<value><string>text</string></value>
								</member>
								<member>
									<name>type</name>
									<value><string>NSString*</string></value>
								</member>
								<member>
									<name>attr</name>
									<value>
										<array><data>
											<value><string>retain</string></value>
										</data></array>
									</value>
								</member>
							</struct></value>
						</data></array>
					</value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```

Note: The output above was retrieved from the following sample class definition:

```
@interface MySampleClass : NSObject {
    BOOL _isNice;
}

@property (readonly) BOOL isNice;
@property (strong) NSString* text;

- (void)printText;
- (void)printSpecialText:(BOOL)wholeText reversedOrder:(BOOL)reverse;

+ (int)staticMethodWithIntReturn;

@end
```



---


## Method: classDumpGetProtocol ##
Returns detailed information about a specific protocol definition

### Request ###
  * **Method Name**: `classDumpGetProtocol`,
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|protocol | string           | No            | -           | Name of the protocol |

**Sample Request**:
```
<methodCall>
	<methodName>classDumpGetProtocol</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>protocol</name>
				<value><string>MySampleProtocol</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs.
Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Class name      |
|staticMethods | Array (of `protocol_method` structs, _see below_) | No            | Class methods   |
|instanceMethods | Array (of `protocol_method` structs, _see below_) | No            | Instance methods |


Struct: **`protocol_method`**
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Method / selector name |
|type     | string           | No            | Return type     |
|required | boolean          | No            | Determines, if a method is mandatory |
|argTypes | Array (von strings) | Yes           | Argument types  |




---


## Method: classDumpSearch ##
Searches the class tree for specific classes, methods, instances and instance variable.

Notes: By default, only class names are searched for the given string. Additional search options must be specified within the request (see below). Searches are performed case-insensitive.

### Request ###
  * **Method Name**: `classDumpSearch`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|string   | string           | No            | -           | Search string   |
|includeClassMethodNames | boolean          | Yes           | true        | In addition, search within names of class methods |
|includeInstanceMethodNames | boolean          | Yes           | true        | In addition, search within names of instance methods |
|includeInstanceVariableNames | boolean          | Yes           | true        | In addition, search within names of instance variables |
|includePropertyNames | boolean          | Yes           | false       | In addition, search within property names |
|includeProtocolNames | boolean          | Yes           | false       | In addition, search within protocol names |

**Sample Request**:
```
<methodCall>
	<methodName>classDumpSearch</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>string</name>
				<value><string>sportschau</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response returns an array of structs.
Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|name     | string           | No            | Class name      |
|protocols | Array (of strings) | Yes           | Name of all protocols, that match the search string |
|staticMethods | Array (of strings) | Yes           | Name of all class methods, that match the search string |
|instanceMethods | Array (of strings) | Yes           | Name of all instance methods, that match the search string |
|instanceVariables | Array (of strings) | Yes           | Name of all instance variables, that match the search string |
|properties | Array (of strings) | Yes           | Name of all properties, that match the search string |



**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>name</name>
					<value><string>SportImRadioTvModel</name></value>
				</member>
				<member>
					<name>instanceVariables</name>
					<value>
						<array><data>
							<value><string>_sportschau</string></value>
						</data></array>
					</value>
				</member>
				<member>
					<name>instanceMethods</name>
					<value>
						<array><data>
							<value><string>sportschau</string></value>
							<value><string>setSportschau:</string></value>
						</data></array>
					</value>
				</member>
			</struct></value>
			<value><struct>
				<member>
					<name>name</name>
					<value><string>Stylesheet</name></value>
				</member>
				<member>
					<name>instanceMethods</name>
					<value>
						<array><data>
							<value><string>SportschauBanner</string></value>
							<value><string>SportschauBannerStyle</string></value>
						</data></array>
					</value>
				</member>
			</struct></value>
			<value><struct>
				<member>
					<name>name</name>
					<value><string>SportschauSettings</name></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```