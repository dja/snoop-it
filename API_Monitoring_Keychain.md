# Monitoring: Keychain #

## Overview ##
Endpoint URI: `/xmlrpc`
Supported Methods:
  * **`keychainGetList`**: Returns a list of all keychain accesses
  * **`keychainGetListUpdate`**: Returns a list of all keychain accesses, starting at a specific ID
  * **`keychainGetId`**: Returns detailed information about a single keychain access
  * **`keychainDeleteAll`**: Removes all entries from the keychain list

---


## Explanation of Response Types ##

### Response Field: `action` ###
| **Key** | **Description** |
|:--------|:----------------|
| 1       | Added a new keychain entry |
| 2       | Modified an existing keychain entry |
| 3       | Deleted an existing entry from the keychain |
| 4       | Read access to a speicifc keychain entry |


### Response Field: `secClass` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Unknown _kSecClass_ |
| 1       | `kSecClassGenericPassword` |
| 2       | `kSecClassInternetPassword` |
| 3       | `kSecClassCertificate` |
| 4       | `kSecClassKey`  |
| 5       | `kSecClassIdentity` |


### Response Field: `accessible` ###
| **Key** | **Description** | **Color** |
|:--------|:----------------|:----------|
| 0       | Unknown _kSecAttrAccessible_ | _(grey)_  |
| 1       | `kSecAttrAccessibleWhenUnlocked` | _(green)_ |
| 2       | `kSecAttrAccessibleAfterFirstUnlock` | _(orange)_ |
| 3       | `kSecAttrAccessibleAlways` | _(red)_   |
| 4       | `kSecAttrAccessibleWhenUnlockedThisDeviceOnly` | _(green)_ |
| 5       | `kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly` | _(orange)_ |
| 6       | `kSecAttrAccessibleAlwaysThisDeviceOnly` | _(red)_   |
| 7       | `kSecAttrAccessibleWhenPasscodeSetThisDeviceOnly` | _(green)_ |

### Response Field: `accesscontrol` ###
| **Key** | **Description** |
|:--------|:----------------|
| 0       | Unknown _kSecAttrAccessControl_ |
| 1       | `kSecAccessControlUserPresence` |



---


## Method: keychainGetList ##
Returns a list of all keychain accesses.

### Request ###
  * **Method Name**: `keychainGetList`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|from     | double           | Yes           | -           | Returns only keychain accesses after a specified date (UNIX Timestamp)  |
|to       | double           | Yes           | -           | Returns only keychain accesses up to a specified date (UNIX Timestamp)  |

**Sample Request**:
```
<methodCall>
	<methodName>keychainGetList</methodName>
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
The response returns an array of structs. Each struct represents an atomic keychain access.

Format:
| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | Unique identifier of each entry in the keychain list  |
|timestamp | double           | No            | Date and time of each entry (UNIX Timestamp)  |
|action   | i4               | No            | Performed action _(see above)_ |
|secClass | i4               | No            | `kSecClass` _(see above)_ |
|accessible | i4               | No            | `kSecAttrAccessible` flag _(see above)_ |
|accesscontrol | i4               | Yes           | `kSecAttrAccessControl` flag _(see above)_ |

**Sample Response**:
```
<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>secClass</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>action</name>
					<value><i4>4</i4></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183677.356116</double></value>
				</member>
				<member>
					<name>accessible</name>
					<value><i4>1</i4></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: keychainGetListUpdate ##
Returns an updated list of keychain accesses, starting at a specific ID. This method can be used to incrementally update the displayed keychain list within a user frontend.

### Request ###
  * **Method Name**: `keychainGetListUpdate`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|lastId   | i4               | No            | -           | Returns only entries with `IDs` greater than `lastId` |

**Sample Request**:
```
<methodCall>
	<methodName>keychainGetListUpdate</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>lastId</name>
				<value><int>2</int></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
Identically equal to **keychainGetList**


---


## Method: keychainGetId ##
Returns detailed information about a single keychain access.

### Request ###
  * **Method Name**: `keychainGetId`
  * **Parameter**: A struct conforming to the following format:
| **Key** | **XML-RPC Type** | **Optional?** | **Default** | **Description** |
|:--------|:-----------------|:--------------|:------------|:----------------|
|id       | i4               | No            | -           | Identifier of the entry |

**Sample Request**:
```
<methodCall>
	<methodName>
		keychainGetId
	</methodName>
	<params><param><value>
		<struct>
			<member>
				<name>id</name>
				<value><int>2</int></value>
			</member>
		</struct>
	</value></param></params>
</methodCall>
```

### Response ###
The response is almost identical to that of `keychainGetList`, but is extended with **`query`** and **`data`** fields according to the following scenarios:

> - Keychain **`data`** (when a keychain item was added)

> - Keychain **`query`** and **`data`** (when a keychain item was modified)

> - Keychain **query** (when a keychain item was deleted)

> - Keychain **`query`** and **`data`**  (when a keychain item was read)

Format:

| **Key** | **XML-RPC Type** | **Optional?** | **Description** |
|:--------|:-----------------|:--------------|:----------------|
|id       | i4               | No            | Unique identifier of each entry  |
|timestamp | double           | No            | Date and time of each entry (UNIX Timestamp) |
|action   | i4               | No            | Performed action _(see above)_ |
|secClass | i4               | No            | `kSecClass` _(see above)_  |
|accessible | i4               | No            | `kSecAttrAccessible` flag _(see above)_  |
|accesscontrol | i4               | Yes           | `kSecAttrAccessControl` flag _(see above)_ |
|query    | struct           | Yes           | Keychain query request |
|data     | struct           | Yes           | Keychain query response |

**Sample Response**:
```

<methodResponse>
	<params>
		<param><value><array><data>
			<value><struct>
				<member>
					<name>secClass</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>id</name>
					<value><i4>2</i4></value>
				</member>
				<member>
					<name>action</name>
					<value><i4>4</i4></value>
				</member>
				<member>
					<name>timestamp</name>
					<value><double>1340183677.356116</double></value>
				</member>
				<member>
					<name>accessible</name>
					<value><i4>1</i4></value>
				</member>
				<member>
					<name>data</name>
					<value><struct>
						<member>
							<name>data</name>
							<base64>c25vb3Bp</base64>
						</member>
					</struct></value>
				</member>
				<member>
					<name>query</name>
					<value><struct>
						<member>
							<name>desc</name>
							<value><string></string></value>
						</member>
						<member>
							<name>svce</name>
							<value><string></string></value>
						</member>
						<member>
							<name>labl</name>
							<value><string></string></value>
						</member>
						<member>
							<name>acct</name>
							<value><string></string></value>
						</member>
						<member>
							<name>gena</name>
							<value><string>Password</string></value>
						</member>
						<member>
							<name>agrp</name>
							<value><string>Q6QFTVE9Y9.com.yourcompany.GenericKeychain</string></value>
						</member>
						<member>
							<name>cdat</name>
							<value><dateTime.iso8601>20120627T14:06:17</dateTime.iso8601></value>
						</member>
						<member>
							<name>mdat</name>
							<value><dateTime.iso8601>20120702T11:00:46	</dateTime.iso8601></value>
						</member>
						<member>
							<name>pdmn</name>
							<value><string>ak</string></value>
						</member>
						<member>
							<name>r_Data</name>
							<value><boolean>1</boolean></value>
						</member>
						<member>
							<name>class</name>
							<value><string>genp</string></value>
						</member>
					</struct></value>
				</member>
			</struct></value>
		</data></array></value></param>
	</params>
</methodResponse>
```


---


## Method: keychainDeleteAll ##
Removes all entries from the keychain access list. ID counter is not resetted.

### Request ###
  * **Method Name**: `keychainDeleteAll`
  * **Parameter**: None

**Sample Request**:
```
<methodCall>
	<methodName>keychainDeleteAll</methodName>
	<params></params>
</methodCall>
```

### Response ###

  * **DONE**, if the method was executed successfully
