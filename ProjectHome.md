# Introduction #
_Snoop-it_ is a tool to assist dynamic analysis and blackbox security assessments of mobile Apps by retrofitting existing apps with debugging and runtime tracing capabilities. _Snoop-it_ allows on-the-fly manipulations of arbitrary iOS Apps with an easy-to-use graphical user interface. Thus, bypassing client-side restrictions or unlocking additional features and premium content of Apps is going to be a child's play.

For more details and background information please refer to our latest presentation on "Pentesting iOS Apps" which was given at the [Shakacon](http://www.shakacon.org/) Security Conference ([Slides](http://de.slideshare.net/Shakacon/andreas-kurtz), [Video](http://www.youtube.com/watch?v=5AZhfBAVgLs)).

A public _beta_ version of Snoop-it is available from the Cydia repository `repo.nesolabs.de`. For a basic overview of installing and using _Snoop-it_, please take a look at the [Getting Started Guide](GettingStarted.md).

To stay up-to-date with the latest news on _Snoop-it_, please follow me on Twitter ([@aykay](http://twitter.com/aykay/)). For a first demonstration of Snoop-it, please have a look at the article [How to Easily Spot Broken Cryptography in iOS Applications](http://www.andreas-kurtz.de/2013/07/how-to-easily-spot-broken-cryptography.html).

Thanks to Markus Troßbach and Sebastian Stocker for their close collaboration on developing Snoop-it!

**iOS 7 Update Notes:** Snoop-it is available on 32-bit platforms. 64-bit iOS devices are currently not supported. Update coming soon.

## Features ##

**Monitoring**

  * File system access (print data protection classes)
  * Keychain access
  * HTTP(S) connections (`NSURLConnection`)
  * Access to sensitive API (address book, photos etc.)
  * Debug outputs (`NSLog`)
  * Tracing App internals (`objc_msgSend`)

**Analysis/Manipulation**
  * Fake hardware identifier (UDID, Wireless MAC, etc.)
  * Fake location/GPS data
  * Explore and force display of available `ViewController`
  * List custom URL schemes
  * List available Objective-C classes, objects and methods
  * Invoke arbitrary methods at runtime
  * Bypass basic jailbreak detection mechanisms

**Other**
  * Simple installation and configuration
  * Easy to use graphical user interface
  * Plenty of filter and search options
  * Detailed description of the [XML-RPC web service interface](API_Monitoring_Filesystem.md)


## Endorsements and Reviews ##

  * "I'm totally impressed by the way Snoop-it has eased down on assessing iOS apps. I managed to identify flaws on client-side implementation in no time. The Method Tracing feature is a blessing!!!" - Arjun Pednekar, NCC Group

  * [iOS Application Security Part 9 – Analyzing Security of iOS Applications using Snoop-it](http://resources.infosecinstitute.com/ios-application-security-part-9-analyzing-security-of-ios-applications-using-snoop-it/) - Prateek Gianchandani, InfoSec Insitute


