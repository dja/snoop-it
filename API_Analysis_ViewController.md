# Analysis / Manipulation: Classes and Methods #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **viewControllerGetTree**: Returns a tree of the current view hierarchy (starting at `UIWindow`)
  * **viewControllerDismiss**: Dismisses/hides a specific ViewController
  * **viewControllerForceDisplay**: Displays a specific ViewController
  * **viewControllerForceDisplayReset**: Restores the initial view hierarchy


---


## Explanation of Request/Response Types ##

### Field: `type` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Unkown          |
| 1       | UIWindow        |
| 2       | UINavigationController |
| 3       | UITabBarController |
| 4       | UIPageViewController |
| 5       | UISplitViewController |
| 6       | UIViewController |
| 7       | UITableViewController |



---


## Method: viewControllerGetTree ##
Returns a tree of the current view hierarchy. Each tree starts with a `UIWindow` root node.

### Request ###
  * **Method Name**: `viewControllerGetTree`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>viewControllerGetTree</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an array of structs. Each struct represents a `UIWindow`.
Every `UIWindow` struct contains one or more structs of Sub-`!ViewControllers`, describing the actual view hierarchy.

Format of the `UIWindow` struct:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | string           | No            | Unique identifier to refer to a specific `UIWindow` in subsequent operations |
|className | string           | No            | Class name of the `UIWindow` |
|isKeyWindow | boolean          | No            | Indicates whether the current `UIWindow` is the app's (main) key window |
|modalVC  | struct conforming to the `UIViewController` format _(see below)_ | Yes           | Modal UIViewController  |
|type     | i4               | No            | Type of the view element _(see above)_ |
|windowLevel | double           | No            | Indicates the position of the `UIWindow` on the view controller stack (x-axis). |
|viewControllers | Array (of `UIViewController` structs) | No            | RootViewController of the `UIWindow` |

Format of the `UIViewController` struct:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | string           | No            | Unique identifier to refer to a specific ViewController in subsequent operations |
|className | string           | No            | Class name of the ViewController |
|title    | string           | Yes           | Titel of the ViewController as displayed within the user interface (if available) |
|displayedModal | boolean          | Yes           | Indicates whether the current ViewController is displayed modally |
|type     | i4               | No            | Type of the view element _(see above)_ |
|canBeDismissed | boolean          | No            | Indicates whether the ViewController can be dismissed (via the method `viewControllerDismiss`) |
|viewControllers | Array (of `UIViewController` structs) | No            | Sub-ViewControllers |
|visible  | boolean          | Yes           | Indicates whether that specific ViewController is currently active and displayed |
|vcIndex  | i4               | Yes           | If the current ViewController is displayed within a container (like e.g. `UINavigationController`, `UITabBarController` etc.), this field  indicates the position of the current ViewController within that container. The greater that number is, the further up is the position of that ViewController on the view stack. |




**Sample Response (Excerpt)**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>isKeyWindow</name>
					<value><boolean>1</boolean></value>
				</member>
				<member>
					<name>type</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>id</name>
					<value><string>0x37df003661568</string></value>
				</member>
				<member>
					<name>windowLevel</name>
					<value><double>0</double></value>
				</member>
				<member>
					<name>className</name>
					<value><string>UIWindow</string></value>
				</member>
				<member>
					<name>viewControllers</name>
					<value>
						<array><data>
							<value><struct>
								<member>
									<name>type</name>
									<value><i4>2</i4></value>
								</member>
								<member>
									<name>id</name>
									<value><string>0x58c34e093074656</string></value>
								</member>
								<member>
									<name>className</name>
									<value><string>MyNavigationController</string></value>
								</member>
								<member>
									<name>canBeDismissed</name>
									<value><boolean>0</boolean></value>
								</member>
								<member>
									<name>viewControllers</name>
									<value>
										<array><data>
										
										...
										
										</data></array>
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


## Method: viewControllerDismiss ##
Dismisses/hides a specific ViewController.

### Request ###
  * **Method Name**: `viewControllerDismiss`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|id       | string           | No            | -           | Identifier of the ViewController that should be dismissed |

**Sample Request**:
```
<methodCall>
	<methodName>viewControllerDismiss</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>id</name>
				<value><string>0x58c34e593986656</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if that ViewController cannot be hidden

**Sample Response**:
```
<methodResponse>
	<params>
		<param>
			<value>
				<string>DONE</string>
			</value>
		</param>
	</params>
</methodResponse>
```


---


## Method: viewControllerForceDisplay ##
Displays a specific ViewController.

### Request ###
  * **Method Name**: `viewControllerForceDisplay`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|id       | string           | No            | -           | Identifier of the ViewController that should be displayed |

**Sample Request**:
```
<methodCall>
	<methodName>viewControllerForceDisplay</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>id</name>
				<value><string>0x58c34e593986656</string></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly

**Sample Response**:
```
<methodResponse>
	<params>
		<param>
			<value>
				<string>DONE</string>
			</value>
		</param>
	</params>
</methodResponse>
```


---


## Method: viewControllerForceDisplayReset ##
Resets all view manipulations that were performed via the method `viewControllerForceDisplay` and restores the initial view hierarchy.

### Request ###
  * **Method Name**: `viewControllerForceDisplayReset`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>viewControllerForceDisplayReset</methodName>
	<params></params>
</methodCall>
```


  * **DONE**, if the method was executed successfully
  * **ERROR**, if the method was _not_ executed correctly

**Sample Response**:
```
<methodResponse>
	<params>
		<param>
			<value>
				<string>DONE</string>
			</value>
		</param>
	</params>
</methodResponse>
```
