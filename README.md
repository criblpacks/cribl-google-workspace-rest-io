# Cribl Google Workspace Rest Collector
----
## About this Pack

This pack is built as a complete SOURCE + DESTINATION solution (identified by the IO suffix). Data collection and delivery happen entirely within the pack's context, eliminating the need to connect it to globally defined Sources and Destinations. 

This Pack is designed to collect, process, and output Google Workspace log data via the Google Workspace REST API. It collects security and audit logs from Google Workspace (formerly G Suite) for threat detection, compliance monitoring, and user activity tracking.

The Pack includes optional Splunk output processing that maps data to Splunk sourcetypes compatible with the [Splunk Add-on for Google Workspace](https://splunkbase.splunk.com/app/5556).  By default, it maps Google Workspace data to the following sourcetypes (set directly in each Collector):

### Reports API Sourcetypes
- Admin console activity logs: `sourcetype=gws:reports:admin`
- User authentication events: `sourcetype=gws:reports:login`
- OAuth token and API access events: `sourcetype=gws:reports:token`
- Google Drive file and folder activity: `gws:reports:drive`
- Google Cloud Platform activity: `gws:reports:gcp`
- Gmail rules and filters activity: `gws:reports:rules`
- Mobile device management events: `gws:reports:mobile`
- Gemini AI usage in Workspace apps: `gws:reports:gemini_in_workspace_apps`
- Chrome browser management events: `gws:reports:chrome`
- SAML SSO authentication events: `gws:reports:saml`
- Google Chat activity: `gws:reports:chat`
- Google admin access to customer data: `gws:reports:access_transparency`
- Context-Aware Access policy enforcement: `gws:reports:context_aware_access`
- Google Data Studio (Looker Studio) activity: `gws:reports:data_studio`

### Alerts Center API Sourcetypes
- Alert Center security alerts: `gws:alerts`

## Deployment

* This pack is configured by default to use the Worker Group's *Default Destination*.
* To use the *Default Destination*: No changes are required. The pack will route the data to the destination currently set as the Default on the Worker Group.
* To use a different Destination: You must update the pack's routes to specify your desired Destination.
* For immediate functionality without requiring Pack route filter expression modifications, every bundled Source within this pack adds a hidden field: `__packsource`. This field allows for seamless routing based on the Pack source.

### Configure the Collectors

#### Create a new project in your Google Cloud Platform deployment.
* Navigate to `https://console.cloud.google.com/projectcreate`. A Google Cloud project is required to use Google Workspace APIs. 
* In the Project Name field, enter a descriptive name for your project.
* The project ID can't be changed after the project is created, so choose an ID that meets your needs for the lifetime of the project.
* Click Create. The Google Cloud console navigates to the Dashboard page and your project is created within a few minutes.

#### Create a Google Cloud Service account in the Google Developers Console.
* From within your newly created project, navigate to APIs and **Services > Library**.
* Search for the Admin SDK API. Select the Admin SDK API.
* In Admin SDK API, select the Enable button to enable the Admin SDK API. Making calls to this API lets you view and manage resources such as users, groups, and audit or usage reports within your domain.
* Navigate to **APIs and Services** > **Credentials**.
* In Credentials, select **Create Credentials > Service account**.
* In Create Service account, perform the following steps:
    * Name your Service account, and select Create and Continue.
    * (Optional) Grant your Service account access to a project.
    * Select Continue.
    * (Optional) Grant users access to your Service account. Select Done.
* In Credentials, select your new Service account name.
* In the Service account details page for your new Service account, perform the following steps:
    * Copy the contents of the Unique ID. (This is also your Client ID)
    * Navigate to the Keys tab.
    * Select **Add Key > Create new key**.
    * Select the JSON key type.
    * Select Create.
    * Save the key type JSON file to your selected directory.
    * (Your new public/private key pair is generated and downloaded to your machine. This is the only copy of the key, so store it securely.)
* Navigate to the Permissions tab.
* Navigate to the user account email address that has Owner permissions. Copy the email address. (This is your email and not the email of the Service account)
* Navigate to **admin.google.com**.
* Log in to your administrator Google account.
* On the Google Admin home page, navigate to Security > API controls.
* In API Controls, navigate to Domain wide delegation, and select Manage Domain Wide Delegation.
* In Manage Domain Wide Delegation, select Add new to add a new client ID.
* In the Add a new client ID window, perform the following steps:
* In the Client ID field, paste the Unique ID that you copied from the Service account details page.
    * In the OAuth scopes (comma-delimited) field, add the below URL's for the scope of the Service account. This gives read-only access when retrieving logs:
    * ***https://www.googleapis.com/auth/admin.reports.audit.readonly***
    * ***https://www.googleapis.com/auth/apps.alerts*** 
* Select Authorize.

#### Update and Schedule the Collectors
* Update the **Service account credentials** with the JSON key obtained from the above steps.
* Update the **Impersonated account's email address** with the email address used to create the Service account. Note: This is NOT the email of the Service account. It needs to have Owner permissions.
* Schedule the Collectors. By default, they run every 5 minutes - adjust as needed.

### Configure your Destination/Update Pack Routes
To ensure proper data routing, you must make a choice: retain the current setting to use the Default Destination defined by your Worker Group, or define a new Destination directly inside this pack and adjust the pack's route accordingly.

### Commit and Deploy
Once everything is configured, perform a Commit & Deploy to enable data collection.

## Pack Configurable Items 
The following are the in-Pack configurable items - review/update them as needed. 

### Lookups
To reduce data volume, there is an optional pipeline `cribl_gws_reductions` (disabled by default) that can be enabled, and in conjunction with the `gws_events.csv` lookup provides options to drop, sample or suppress certain gws events based on the value in the action field. Update the event_action field with `drop`, `sample`, or `suppress` as required. All events are set to `keep` by default.

### Variables

The Pack has the following variables:

`default_splunk_index`: Default index for the Splunk output - defaults to `gws`. Please update to match your Splunk index for gws data.
`default_splunk_sourcetype`: Default sourcetype for the Splunk output - defaults to `gws:events`. The sourcetype value will fall back to this value if there is no sourcetype specified in the Collector configuration. 

## References

- [Google Workspace Admin SDK](https://developers.google.com/admin-sdk)
- [Alert Center API](https://developers.google.com/admin-sdk/alertcenter)
- [Reports API](https://developers.google.com/admin-sdk/reports)

## Upgrades

Upgrading certain Cribl Packs using the same Pack ID can have unintended consequences. See [Upgrading an Existing Pack](https://docs.cribl.io/stream/packs#upgrading) for details.
## Release Notes

### Version 1.0.1
* Bug fix: Implemented tighter lookup restraints in the `cribl_gws` pipeline to allow for duplicate action values in the `gws_events.csv` lookup

### Version 1.0.0
* Initial release

## Contributing to the Pack

To contribute to the Pack, please connect with us on [Cribl Community Slack](https://cribl-community.slack.com/). You can suggest new features or offer to collaborate.

## License
This Pack uses the following license: [Apache 2.0](https://github.com/criblio/appscope/blob/master/LICENSE).
