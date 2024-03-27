# iDelta Add-on for Google Analytics

> The iDelta Add-on for Google Analytics uses the Google Analytics Data API (GA4) to fetch and ingest it into Splunk.

## Introduction

There have been various solutions over time to ingest Google Analytics data into Splunk but none had been kept up to date and there doesn't appear to be any that work with the relatively new GA4 platform.  This add-on was built to bridge that gap. 

The Add-on allows the user to create inputs where they specify the google analytics metric required (e.g. totalUsers) and the dimensions to split the data by. 

In order to make the API call, a private key is required and it needs to be stored within the bin directory.  For on-prem Splunk installations this should not present an issue but Splunk Cloud customers should give consider where to run the add-on and if the answer is "on Splunk Cloud" then discuss with Splunk support how to achieve this.

## Pre-Requisites

Further details on these steps are listed below:

1. A website integrated with Google Analytics (GA4)
2. Private Key of a Google service account, with access to the GA4 data
3. The [Google Analytics property](https://developers.google.com/analytics/devguides/reporting/data/v1/property-id) id of the website you want to retrieve data from
4. An installation of this add-on, with Internet access
5. A list of metrics and dimensions to retrieve

## Setup
### Google Analytics

The Google Analytics adminstrator should complete the following steps to generate a private key for use by the add-on:

1. Follow Steps 1 and 2 from the [API Quick Start](https://developers.google.com/analytics/devguides/reporting/data/v1/quickstart-client-libraries)
2. Provide the credentials.json file to the Splunk Admin
3. Also provide the [Property ID](https://developers.google.com/analytics/devguides/reporting/data/v1/property-id) of the site being monitored by Google Analytics - this is a 9 digit number, visible from the Admin section (gear icon bottom left) and then Property > Property Details 

Note that instead of using the quick start method above you can [manually create an OAuth client ID](https://support.google.com/cloud/answer/6158849#zippy=%2Cservice-accounts%2Cpublic-and-internal-applications) and then assign the permissions as per step 2 in the Quick Start.  This provides more control but involves more steps.

### Splunk Add-on Installation

To install the add-on, on the Splunk server that will host the add-on:
1. Create a directory $SPLUNK_HOME/etc/apps/idelta_addon_for_google_analytics
2. Copy (recursively) the contents of the package directory in this repository into that directory
3. Place the credentials.json file, supplied by the Google Analytics administrator, into the following location (note that name change on the file): $SPLUNK_HOME/etc/apps/idelta_addon_for_splunk/bin/google_analytics_credentials.json
4. Ensure the above file has appropriate ownership and permissions set (e.g. chown splunk.splunk, chmod 400)
5. Restart the Splunk server (or reload, requires testing)

Note that this add-on has been built using the ucc framework, and the repository contents are orientated towards development - that is the reason that the add-on currently sits under a "package" directory.

### Splunk Add-on Configuration

To configure the add-on:
1. Optional: setup a new Splunk index for your data
2. In the add-on:
   - Click on Configuration > Accounts then click Add
   - Enter a name for the account (e.g. which website is it for)
   - Enter the Google Analytics Property ID
3. Select the Inputs tab:
   - Click Create New Input
   - Enter a name for the input (e.g. activeUsers_myWebSite)
   - Enter the [Metric Names](https://developers.google.com/analytics/devguides/reporting/data/v1/api-schema#metrics) required (e.g. activeUsers)
   - Enter the [Dimension Names](https://developers.google.com/analytics/devguides/reporting/data/v1/api-schema#dimensions) (e.g. city, country, browser)
   - Enter a start date (e.g. yesterday)
   - Enter an end date (e.g. today)
   - Enter an interval in seconds (e.g. 86400)
   - Select the Account to Use (as setup in step 2 above)

Note that metric name, dimensions, start date and end date should use the same terms as specified in the Google API documentation, see [startDate and endDate definitions](https://developers.google.com/analytics/devguides/reporting/data/v1/rest/v1beta/DateRange)
