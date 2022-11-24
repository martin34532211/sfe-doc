# User Guide

## What’s new

You can now work together on the same Thing with other users. To get access to this feature, your admin will need to add each user to a security group. For more information, see the  “API Guide” and the “Administrator Guide”.

### Commentary for the above

- I’ve decided not to burden the user guide with too much technical detail, but included a link to the other guides for those users that want to know more.
- This guide would also need a section called "Collaborating with other users", explaining how to share a Thing with other users, and how to work together on the same Thing.
- To help encourage adoption, I've added a hint that users can contact their admins to get access.

# Administrator Guide

## What’s new

This release allows multiple users to work on the same Thing. This is done using the new Streaming frontend (SFE), which quickly propagates changes to all clients. The following subsections explain how you can prepare for this new functionality.

### Performance considerations for SFE

The new SFE and Streaming API processes can affect system performance. By default, one SFE is created on startup, with an additional SFE started in parallel for every 10 Streaming API clients.

To understand the server impact of SFE, you can monitor the resource usage of the `_sfe` and `_stream` processes, which are not expected to consume more than 50% of the total system load. If this threshold is exceeded, you can restart the `_sfe` instances using the following command:

```bash
$ widgetapp_admin-process_sfe -n <num_instances> -start
```

Once the process has completed, the SFE will automatically restart the terminated `_stream` processes for any existing connections.

### Networking changes for SFE

External systems connect to SFE using `wss://www.widgetapp.com/_stream`. To receive stream messages from any of the `widgetApp_useraction_*` APIs, external systems must listen on port 200.

The Streaming API uses websockets for connectivity. This is multiplexed so that multiple services can use the same websocket connection.

### Managing access to the Streaming API

To access the Streaming API, user groups must be authorized by an OAuth 2.0 access token. This is configured using the WidgetApp frontend (WFE).

### Commentary for the above

- This is where I’ve grouped the configuration-related changes that administrators will need to know.
- Ideally, the procedure for restarting the SFE processes should be in a separate troubleshooting section. The “What’s new” section can still link to this section to make the admins aware of this option. 
- I’m also wondering if the `widgetapp_admin-process_sfe -n <num_instances> -start` command is correct, because the parameters seem to create new instances, rather than restarting existing ones. I’d want to check this with engineering, or test it out myself in a lab, if possible.
- I’ve removed the `Authentication is unchanged from previous versions - managed through the existing WidgetApp frontend (WFE).` note, as it seems superfluous. Unless there was a reason admins were anticipating that this might change?
- I’d want to add a section that specifically explains how to use the WFE to grant Streaming API access to a user group.

# API Guide

## What’s new

This release allows multiple users to work on the same Thing. This is done using the new Streaming frontend (SFE), which quickly propagates changes to all clients.

- There are no changes to the existing API requests.
- The majority of user activities for SFE use AJAX. For example, searching or scrolling are done through `_ajax/` endpoints. For more information, see the Streaming API reference section.
- To use the new Streaming API, existing API requests can handover authorized connections to the new Streaming frontend (SFE). The SFE then manages the session, with additional Streaming API capabilities.
- To access the Streaming API, user groups must be authorized by an OAuth 2.0 access token. For information on how to configure this access, see the “Administrator Guide”.

### Commentary for the above

- I thought it was important to include the “There are no changes to the existing API requests.” line early on, as a reassurance to the devs.
- If the `widgetApp_useraction_*` APIs aren't already documented, we could add them by refering to an OpenAPI specification.
- Admins and devs would both need to know about the OAuth 2.0 access token, so I’ve included it in both guides.
