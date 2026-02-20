![](img/banner_accounts.png)
# How To Create a CORIOLIX Account

At present, most of the CORIOLIX web user interface is available for unauthenticated anonymous users.  However, some core functionality is restricted to named local users who must first register for an account, then login to gain access to the restricted content.

## Restricted Content:
Restricted content under each CORIOLIX navigation heading include:

* **Cruise Charts** - *logged in users may save custom user drawn routes*
* **Sensor Plots**  - *there are no restricted items under this heading*
* **Status** - *there are no restricted items under this heading*
* **Sensors**
    - **Add/Modify** - *add new sensors or make edits to sensor configurations*
    - **Sensor Logs** - *view, edit, or download sensor specific logfiles*
    - **Vendors** - *view or edit sensor vendor metadata*
* **Data** - *there are no restricted items under this heading*
* **Documents**
    - **Edit** - *upload or change document metadata*
* **Cruises**
    - **Add New Cruise** - *edit cruise level metadata*
    - **Event Log** - *view, create, or download events & event log*
* **My Account**
    - **My Profile** - *edit your account settings*
    - **My Alerts** - *configure custom alerts*
    - **Change My Password** - *reset password*
* **About** - *there are no restricted items under this heading*

## User Scope:
Currently, accounts are managed on a per-vessel basis. That means accounts are not synchronized across the various CORIOLIX vessel specific deployments. So, if users want access to multiple CORIOLIX user interfaces, Endeavor and Oceanus for example, a separate account on both systems is needed.

Account information is synchronized between the ship side CORIOLIX installation and the shoreside CORIOLIX installation so users only need one account per vessel. The same login credentials are used for both instances. 

## First Time Account Creation
Browse to the appropriate CORIOLIX site.

From the navigation options at the top of the page, select: *My Account > Create New Account*

![](img/UserAccountMyAccount.png)

Complete the User Profile setup form and select the *Submit* button. Note the email and password used. Use these credentials to access CORIOLIX.

![](img/UserProfileMyAccount.png)

## Setting Up User Specific Alerts:

From the site navigation options select: *My Account > My Alerts*

![](img/UserAlertsMyAccount.png)

#### Alert Types:
* Value Alerts - Value alerts work by evaluating sensor parameter value against a threshold condition. The Alert Configuration page has an example of a use case for Value Alerts. Users may combine up to two sensors or sensor parameters per value alert.
* Proximity Alerts - Proximity alerts evaluate the location of the vessel against geographic points of interest (stations, ports, etc.).
* Temporal Alerts - Temporal alerts evaluate the time now against a datetime parameter for a sensor (e.g. calibration date, warranty expiration date, schedule maintenance data, etc.) to monitor.  Users may set an additional parameter, offset, to receive advance notice of a pending event.
* Sensor Alerts - These alerts serve as status updates for sensors. Users can configure them to alert when a sensor hasn't collected data in a given timeframe. 

Place a check next to any alerts (in the alert table) that you would like to receive.  Existing alerts may be edited using the "Edit" button.  New alerts may be created by using one of the "Create New" buttons.

When your selections have been made, click the "Subscribe to Selected Alerts" button to update your subscriptions.
