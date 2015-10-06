# Other: Basic App Info #

## Overview ##
Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`appInfoGetInfo`**: Returns some basic information about the App under review
  * **`appInfoGetURLHandlers`**: Returns a list of URL handlers provided by the App


---


## Method: appInfoGetInfo ##
Returns some basic information about the App.

### Request ###
  * **Method Name**: `appInfoGetInfo`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>appInfoGetInfo</methodName>
	<params></params>
</methodCall>
```

### Response ###

The response returns an **array** of **structs**. Each struct returns a specific piece of information.

Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|bundleIdentifier | string           | No            | Bundle Indentifier of the App |
|displayName | string           | No            | Displayed Name  |
|architecture | string           | No            | Platform Architecture (32-bit or 64-bit) |
|iconFile | string           | No            | URL to download the App Icon |
|version  | string           | Yes           | Version of the App |
|minimumOSVersion | string           | Yes           | Required Minimum OS Version |
|buildVersion | string           | Yes           | Build-Version of the App |
|hasPIE   | boolean          | No            | Binary compiled as Position-independent Executable _(PIE)_? |
|hasARC   | boolean          | Yes           | Binary compiled with Automatic Reference Counting _(ARC)_? (requires _odcctools_) |
|hasSSP   | boolean          | Yes           | Binary compiled with Stack Smashing Protection _(SSP)_? (requires _odcctools_) |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>minimumOSVersion</name>
					<value><string>7.0</string></value>
				</member>
				<member>
					<name>buildVersion</name>
					<value><string>1.4.0</string></value>
				</member>
				<member>
					<name>bundleIdentifier</name>
					<value><string>com.sic.siceventinfo.etm.tgp</string></value>
				</member>
				<member>
					<name>displayName</name>
					<value><string>Truck-GP</string></value>
				</member>
				<member>
					<name>iconFile</name>
					<value><string>/appIcon.png</string></value>
				</member>
				<member>
					<name>hasPIE</name>
					<value><boolean>0</boolean></value>
				</member>
				<member>
					<name>architecture</name>
					<value><string>32-bit</string></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: appInfoGetURLHandlers ##
Returns a list of URL handlers provided by the App.

### Request ###
  * **Method Name**: `appInfoGetURLHandlers`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>appInfoGetURLHandlers</methodName>
	<params></params>
</methodCall>
```

### Response ###
The response returns an **array** of **structs**. Each struct returns detailed information on a supported URL handler.

Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|role     | string           | Yes           | The app’s role with respect to the URL type (see `CFBundleURLTypes` in [iOS Documentation](http://developer.apple.com/library/ios/#documentation/general/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html%23//apple_ref/doc/uid/TP40009249-SW1)) |
|iconFile | string           | Yes           | URL to download the icon image file of the URL-Handler |
|name     | string           | Yes           | Description of the URL handler |
|urlSchemes | Array (of strings) | Yes           | Types of URL schemes (like e.g. `http`, `ftp`, `mailto`, ...) |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>name</name>
					<value><string>iTunes App Store URL</string></value>
				</member>
				<member>
					<name>urlSchemes</name>
					<value>
						<array><data>
							<value><string>itms-apps</string></value>
							<value><string>itms-appss</string></value>
						</data></array>
					</value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```
