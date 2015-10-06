# Getting Started #

In order to install and run _Snoop-it_, please perform the following steps (please make sure, that _MobileSubstrate_ has been installed from Cydia):

  1. Add the Cydia repository `repo.nesolabs.de` and install the provided _Snoop-it_ package
    * Go to the "Manage" Tab in Cydia
    * Click the "Edit" button
    * Click the "Add" button
    * Enter the URL `http://repo.nesolabs.de/` and click the "Add Source" button
    * After Cydia finished updating, click the "Return to Cydia" button
    * Choose the new repository "NESO Security Labs GmbH"
    * Choose "Snoop-it"
    * Click the "Install" button
    * Click the "Confirm" button
  1. After installing, run the _Snoop-it Configuration App_ by tapping the _Snoop-it_ icon on your SpringBoard
  1. Using the _Snoop-it Configuration App_, please select the Apps (System/Cydia/AppStore) to analyze
  1. Adjust some _Snoop-it_ settings (like e.g. the listening port of the web interface, authentication, tracing etc.)
  1. Run the selected App & point your browser to the _Snoop-it_ web interface.

If you run into problems that Snoop-it appears not to be injected, please make sure to [completely terminate](http://support.apple.com/kb/ht5137) the app under review and restart it.


Note: The Snoop-it package has been signed. If you want to verify the signature before installing, please add our [public key](http://repo.nesolabs.de/NESOSecurityLabsCydiaRepo.asc) using the following command: `apt-key add NESOSecurityLabsCydiaRepo.asc`. After importing this key, you should find the message "This package has been signed" under the section "Package Details"

A documentation of the [XML-RPC web service API](API_Monitoring_Filesystem.md) is provided within this wiki. You might make use of this API in order to automate tests or just to build your own graphical user interface.