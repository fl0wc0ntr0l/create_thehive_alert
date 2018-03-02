# NO LONGER SUPPORTED
This project is no longer supported. I do not use theHive as my primary incident response platform anymore. This code is provided for the community that does. I cannot make any guarantees as to whether this will work for your version of Splunk or theHive. What I can tell you is that this was working on theHive 2.13.2 and Splunk 6.5.1 *in my environment* and that is all I cared about. Please do not raise issues or ask for a wiki. You will not get them from me. 

This app can be found in Splunkbase here: https://splunkbase.splunk.com/app/3642/


# Create theHive Alert
A package designed to enable creating alerts in theHive, a popular open-source incident response platform, as an Alert Action from Splunk. Simply drop in the package and configure your theHive alerts on a per-alert basis in Splunk.

To use this package correctly, let's go over how exactly this AA works.

### How Splunk Custom AAs work
Splunk custom alert actions make use of a flat CSV file that Splunk creates automatically and delivers to the alert action script as part of the payload. This suits our purposes well - all we have to do is create a CSV file with the columns we want, parse the CSV, and create an Alert based on that data. You can include whatever fields you like, but you need a unique identifier that can bring you back to that event, and the time the event occurred. For the most part, the best way to do this is the __table__ command in Splunk, which creates an easy-to-read CSV with the fields you specify and also runs very quickly.

# Configuration breakdown
+ URL - the URL for the Alert API of your instance of theHive.
+ Case Template - the case template to use for Alerts if/when you import them to a Case. Use the name of an existing case template, or leave blank for template "default".
+ Type - Specify an alert type (OSINT, Network, Login, etc.) Defaults to "alert".
+ Source - Specify an alert source. Defaults to "splunk".
+ Unique ID - a unique identifier specific to the event. This is a field name present in your data. The value of this field will be used for the sourceRef field of the alert and is required. If your event data does not generate unique identifiers per event, an easy out is to use "eval Event\_ID=md5(\_raw)" and then use __Event_ID__ as your Unique ID field. 
+ Time - A time field in epoch format to use for the Alert creation time. This one is easy - just use Splunk's built-in ___time__ field.
+ Title - the title of the created Alert. Uses the title of the Splunk alert by default.
+ Description - The description to add to the created Alert. Uses the description of the Splunk alert by default.
+ Tags - A comma-separated list of values to use as tags for the created Alert. Do not use quotes or spaces.
+ Severity - Set an Alert's Severity. Choose one of Low, Medium or High.
+ TLP - Set an Alert's TLP. Choose from White, Green, Amber, or Red.
+ Ignored Fields - Comma-separated list of field names to exclude from Artifacts of the alert. Do not use quotes or spaces.

### Alert Action pseudocode
```
check execution mode
	read payload from stdin
	extract configuration from payload
	extract filepath from payload
	test if filepath exists
		test if file can be opened
			create a DictReader on the CSV
			for row in CSV create an alert with the key-value pairs as artifacts and other fields as specified in the configuration
```

## Required Configuration
After installing the app, you will have to modify the Python script it uses in order to successfully create alerts. Modify the username and password to one that exists in your instance of theHive and has at least write-level permissions. No further modification should be necessary.   
