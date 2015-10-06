# Monitoring: File System #

## Overview ##

Endpoint URI: `/xmlrpc`

Supported Methods:
  * **`filesystemGetAccessList`**: Returns a list of all files and directories accessed since the App was started
  * **`filesystemGetAccessListUpdate`**: Returns an updated list of accessed files and directories, starting at a specific `ID`
  * **`filesystemDeleteAll`**: Removes all entries from the file system list


---


## Explanation of Response Types ##

### Response Field: **`action`** ###
| **Key** | **Description** |
|:--------|:----------------|
| 1       | File was created |
| 2       | `NSFileProtectionClass` was changed |


### Response Field: **`class`** ###
| **Key** | **Protection Class** | _**Color**_ |
|:--------|:---------------------|:------------|
| 1       | `NSFileProtectionNone` | _(red)_     |
| 2       | `NSFileProtectionComplete` | _(green)_   |
| 3       | `NSFileProtectionCompleteUnlessOpen` | _(orange)_  |
| 4       | `NSFileProtectionCompleteUntilFirstUserAuthentication` | _(orange)_  |

### Response Field: **`accessMode`** ###
| **Key** | **Access Mode** |
|:--------|:----------------|
| 0       | Unknown / Error |
| 1       | Read-only       |
| 2       | Write-only      |
| 3       | Read and Write  |


### Response Field: **`sandbox`** ###
| **Key** | **Sandbox** |
|:--------|:------------|
| 0       | Access outside of the App-Sandbox |
| 1       | Access within the App-Sandbox |

### Response Field: **`type`** ###
| **Key** | **Type** |
|:--------|:---------|
| 0       | File     |
| 1       | Directory |


---

## Method: filesystemGetAccessList ##

Returns a list of all files and directories accessed as well as their NSFileProtectionClasses.

### Request ###
  * **Method Name**: `filesystemGetAccessList`
  * **Parameter**: A `struct` conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
| from    | double           | Yes           | -           | Returns only file system accesses after a specified date (UNIX Timestamp) |
| to      | double           | Yes           | -           | Returns only file system accesses up to a specified date (UNIX Timestamp) |

**Sample Request**:
```
<methodCall>
	<methodName>filesystemGetAccessList</methodName>
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
The response returns an **array** of **structs**. Each struct represents an atomic file system access or a change of a file's `NSFileProtectionClass`.

Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | Unique identifier of each entry in the file system list |
|timestamp | double           | No            | Date and time of each entry (UNIX Timestamp) |
|path     | string           | No            | Path which was accessed |
|action   | i4               | No            | Performed action _(see above)_ |
|class    | i4               | No            | NSFileProtectionClass _(see above)_ |
|accessMode | i4               | Yes           | Access Mode _(see above)_ |
|otherFlags | string           | Yes           | Additional open flags |
|sandbox  | i4               | No            | Access within the App-Sandbox? _(see above)_ |
|type     | i4               | No            | File or directory _(see above)_ |

Note: Files can even be downloaded via XMP-RPC: `http://DEVICE_IP/download-file/[PATH]`

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>class</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>action</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183677.356116</double></value>
				</member>
				<member>
					<name>path</name>
					<value><string>/System/Library/CoreServices/SystemVersion.plist</string></value>
				</member>
				<member>
					<name>sandbox</name>
					<value><i4>0</i4></value>
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


## Method: filesystemGetAccessListUpdate ##

Returns an updated list of accessed files and directories, starting at a specific `ID`. This method can be used to incrementally update the displayed file list  within a user frontend.

### Request ###
  * **Method Name**: `filesystemGetAccessListUpdate`
  * **Parameter**: A `struct` conforming to the following format:

| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
| lastId  | i4               | Yes           | -           | Reponse contains only entries with `IDs` greater than `lastId` |

**Sample Request**:
```
<methodCall>
	<methodName>filesystemGetAccessListUpdate</methodName>
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
Identically equal to **`filesystemGetAccessList`**



---


## Method: filesystemDeleteAll ##
Removes all entries from the file system list. `ID` counter is not resetted.

### Request ###
  * **Method Name**: `filesystemDeleteAll`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>filesystemDeleteAll</methodName>
	<params></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
