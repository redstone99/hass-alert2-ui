<!-- ~/tmp/general-env/bin/grip -b ~/tmp/hass-alert2-ui/README.md 6420 -->

[![GitHub Release](https://img.shields.io/github/v/release/redstone99/hass-alert2-ui)](https://github.com/redstone99/hass-alert2-ui/releases)
[![GitHub Release Date](https://img.shields.io/github/release-date/redstone99/hass-alert2-ui)](https://github.com/redstone99/hass-alert2-ui/releases)
[![GitHub commit activity](https://img.shields.io/github/commit-activity/y/redstone99/hass-alert2-ui)](https://github.com/redstone99/hass-alert2-ui/commits/master/)
<!-- ![GitHub commits since latest release](https://img.shields.io/github/commits-since/redstone99/hass-alert2-ui/latest) -->

# Alert2 UI

This repository contains two Lovelace cards to display and interact with [Alert2](https://github.com/redstone99/hass-alert2) alerts.  It also enhances the information shown in the "more-info" dialog when viewing Alert2 entities in entity cards. We recommend first installing [Alert2](https://github.com/redstone99/hass-alert2).

![Alert2 overview card](resources/overview.png)
![Alert2 manager card](resources/manager.png)

## Install

<details>
<summary>HACS install (recommended)</summary>

1. If HACS is not installed, follow HACS installation and configuration instructions at https://hacs.xyz/.

1. Click the button below

    [![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=redstone99&repository=hass-alert2-ui&category=plugin)

    or visit the HACS pane, type **Alert2 UI** in the search field, expand "New" or "Available for download" if necessary, and click on **Alert2 UI** to get to the details page.

    If, for some reason, Alert2 UI does not show up as a HACS search result,  you can add Alert2 UI manually by adding  `https://github.com/redstone99/hass-alert2-ui.git` as a custom repository of type "Dashboard" by following [these instructions](https://hacs.xyz/docs/faq/custom_repositories/).

1. The UI should now show the "Alert2 UI" doc page in HACS. Click the "Download" button (bottom right of screen) to download the UI module.

    If for some reason adding the repository did not take you to the "Alert2 UI" doc page, you may need to click again on the HACS pane, search for "Alert2 UI" and click on it to get to the page.


1. Click "Reload" when it prompts you to reload your browser.
</details>

<details>
<summary>Manual install</summary>

1. Download the `Source code (zip)` link from the repository [release section](https://github.com/redstone99/hass-alert2-ui/releases) under "Assets" and unzip it.

   We do not recommend downloading directly from the `main` branch.

1. Create the directory `www` in your Home Assistant configuration directory if it doesn't already exist.

   Your configuration directory is the directory with `configuration.yaml`. It is commonly `/config`, or may be something like `~/.home-assistant/` for Linux installations.
   
1. Copy `alert2.js` from unzipped file into the directory `www` in your config.

   Your config directory should look similar to this after copying:
                   
        <config dir>/configuration.yaml
        <config dir>/www/alert2.js
        <config dir>/custom_components/alert2/__init__.py
        <config dir>/custom_components/alert2/sensor.py
         ... etc...

</details>

<details>
<summary>Upgrading</summary>

After upgrading Alert2 UI, it can take time for the browser or HA companion app to update alert2.js.  To speed that up:

* On the web, try reloading the page a few times and also "Clear cookies & site data".

* On the Android HA companion app, try going to System Settings -> Apps -> Home Assistant -> Storage & cache and clicking "Clear cache".  Then in the HA app, click on the vertical dots at the top and click on "Reload resources".  You may need to wait a few seconds and click "Reload resources" twice.

</details>

After installing or upgrading, you can verify the Alert2 UI version you're running by clicking on the "Alerts" header of the Lovelace card:

![Alert2 overview card with version info](resources/overview2.png)


## Setup

The install process above downloaded a js module, `alert2.js`.
<br>Next:

1. If you installed Alert2 UI through HACS and configure dashboards via the UI (i.e., Lovelace in `storage` mode), then `alert2.js` should be loaded into HA automatically.

    Otherwise:

    If you configure dashboards via yaml (Lovelace in `yaml` mode), you need to add an entry for `alert2.js` in the resources subsection of the lovelace section of your `configuration.yaml`. If you installed via HACS, this will look like (in bold):

    <pre>lovelace:
      mode: yaml
      resources:
        <b>- url: /hacsfiles/hass-alert2-ui/alert2.js</b>
          <b>type: module</b>
        ...</pre>

    or if you installed manually directly into the `www` directory, it will look like:

    <pre>lovelace:
      mode: yaml
      resources:
        <b>- url: /local/alert2.js</b>
          <b>type: module</b>
        ...</pre>

    Lastly, if you installed Alert2 UI manually and configure dashboards via the UI, then enable "Advanced mode" in your user profile, then click on Settings -> Dashboards -> Resources.  "Resources" may appear only in the triple vertical dots on the upper right of the dashboards page. Click on "Add Resource" to add `alert2.js`.


1. `alert2.js` defines two custom UI cards called "Alert2 Overview" and "Alert2 Manager". If you configure dashboards via the UI, start editing a dashboard, then click on "Add Card" (or the "+" icon), then search for either card and click to add.

    If you're using yaml to specify a dashboard, you can add the Alert2 Overview and Alert2 Manager card to your dashboard by adding it to the list of cards in a view, like (in bold):

    <pre>views:
    - title: Monitoring
      name: Example
      cards:
      <b>- type: "custom:alert2-overview"</b>
      <b>- type: "custom:alert2-manager"</b>
      - type: entities
        ...</pre>


1. Restart HomeAssistant and reload the UI

## Overview card

The `alert2-overview` Lovelace card lists recently active Alert2 alerts, as well as snoozed or disabled alerts.  A slider at the top of the card controls the time window covered.

Each line shows the status of the alert, including when it last fired, how many times it fired since the last notification, and whether it has been ack'ed, snoozed or disabled.  Each alert will show an "ACK" button if it hasn't been acked already.

The badge shown at the left of the alert will be colored based on priority and status. A condition alert that is on, or a condition alert that has not yet been acked and `ack_required` was set, or an event alert that has not yet been acked will be colored based on priority. The default is blue for low, orange for medium and red for high.  Otherwise the badge will be grey.  These colors may be customized (see "Config", below).

The button "ACK ALL" will ack all alerts, not just the ones displayed.

Note - `alert2-overview` will show currently firing old Alert-1 alerts, but it will not show recent activity for the old alerts.

![Alert2 overview card](resources/overview.png)

The order of displayed alerts is:
1. Alerts currently on and unacked, sorted by priority and then recency.
1. Alerts currently off and unacked, sorted by priority and then recency. This includes event alerts.
1. Alerts currently on and acked, sorted by priority and then recency.
1. Alerts currently off and acked, sorted by priority and then recency. This includes event alerts.

For the purpose of ordering, alerts that are snoozed or disabled are treated as if acked.

If alert A is superseded by another alert that's firing, alert A will appear in UI without a badge, directly below the alert that supersedes it.

### Config

The Overview card supports a few configuration options:

| Name | Description |
|---|---|
| `title` | Text to display at top of Overview card. Defaults to "Alerts" |
| `include_old_unacked` | A boolean incidating whether to always show unacked alerts, even if they have fallen out of the display time window.  Can be truthy string values like "true", "on", "yes", or the opposites. Defaults to false. |
| `filter_entity_id` |  A glob or regex filter that restricts the candidate set of Alert2 entities to be displayed. A glob is a string with \* in it at least once.  A regex is a string that begins and ends with "/". Defaults to "*" (i.e., do not exclude any entities) |
| `hide_superseded` | When truthy (string values like "true", "on", or "yes"), do not display in the Overview card any alert that is superseded by a currently firing alert. |
| `low_priority_color` | A string representing the CSS color value used for the badge of low-priority alerts. Default is "blue". |
| `medium_priority_color` | A string representing the CSS color value used for the badge of medium-priority alerts. Default is "orange". |
| `high_priority_color` | A string representing the CSS color value used for the badge of high-priority alerts. Default is "red". |
| `off_color` | A string representing the CSS color value used for the badge of alerts that are off or have been acked. Default is "grey". |

Example Lovelace YAML config:

    views:
    - title: Monitoring
      name: Example
      cards:
      - type: "custom:alert2-overview"
        title: House Alerts
        include_old_unacked: yes

        # Glob filter
        filter_entity_id: "alert2.house_*"
        #
        # OR
        # Regex filter
        filter_entity_id: "/house[12]_/"
    
The same config options are available in the UI itself.  When you add the Overview card, you can directly edit the "code" in YAML to specify options:

    type: custom:alert2-overview
    title: House Alerts
    filter_entity_id: ...



### Detailed alert info

If you click on a specific alert listed in the alert overview, a dialog pops up with detailed info on the alert and notification controls. Example:

![Alert2 overview card](resources/more-info.png)

The first line is a repeat of the alert status.

The second "Previous Firings" section lists each firing over the previous 24 hours, limited to the most recent 20 events.  The time when the alert turned on or off is listed as well as the message template text rendered when the alert fired.  The "PREV" button lets you go back further in time and "RESET" returns the listing to the firings over the past 24 hours and refreshes the listing.

The "Notifications" section lets you snooze or disable notifications. Select an option and click "Update".  The "Status" line will update dynamically.

Snoozing an alert implicitly acks it once and prevents notifications during the snooze interval.  When the interval ends you can opt to get a summary notification of any alert activity during the period. See `summary_notifier` in the [Alert2](https://github.com/redstone99/hass-alert2) docs.

Times are displayed in the browser local time zone.

### Other ways to view alerts

You may also add alert2 entities to entities cards and other cards that support entities.  If you click on an alert shown in such a situation, you'll see a popup (called a "more-info dialog") similar to the one shown above.  However, since Alert2 isn't integrated into the core HomeAssistant, that dialog will include some extra default sections like "history", but will also include the sections described above.

Finally, you can also view alert information by going to Settings -> Devices & Services -> Entities, typing "alert2." in the search box and browsing the list.  Clicking on one will popup the "more-info dialog".

## Manager card

The `alert2-manager` Lovelace card allows you to adjust default settings, create/edit/delete alerts, and search over alerts created via the UI.  The search box lets you filter UI-created alerts by the text you type. Clicking on any result will bring up a dialog that lets you edit the alert.

<img src="resources/search.png" width="500"/>

Any defaults adjusted via the UI override any defaults specified in your YAML config. The defaults apply to alerts created either via the UI or in your YAML config.  After adjusting defaults, you can reload the alert config to have the new defaults apply to all alerts, whether defined in YAML or via the UI.  You can reload the config by going to "Developer Tool" -> "YAML" and clicking on "Alert2".

Alert2 does not allow any two alerts created via the UI or YAML to have the same domain and name.

### Editing config fields

When editing a config field for defaults or an alert, a line will appear with "Render result", showing how Alert2 interprets what you've written.

![Editing config fields](resources/edit-field.png)

You can also click on the config field name to show some help info, including examples of what you can enter:

![Editing config help](resources/edit-help.png)

Most fields are interpreted as YAML, with the exception that if you enter template characters (e.g., "{{" ), then the input is automatically quoted, to simplify typing.

## Contributing

We welcome ideas for improving Alert2 UI or help implementing any of the great ideas people have suggested so far. A number of great ideas have arisen on the main [development thread](https://community.home-assistant.io/t/alert2-a-new-alerting-component). Feel free to jump in on any of the threads!

### Testing

To run the tests, you must first run the [dummy server](https://github.com/redstone99/hass-alert2#testing) from hass-alert2.  Then you can run the tests in your browser by visiting:

    http://localhost:50005/jtest/tests/t.html
    http://localhost:50005/jtest/tests/t2.html

Both pages should display "Done" when they are done.  You may need to show the console logs while running to slow the test down a bit to get them to pass.  A number of sections rely on sleeping to ensure an update has occurred (TODO - replace sleeps with waits on DOM content to show up).
