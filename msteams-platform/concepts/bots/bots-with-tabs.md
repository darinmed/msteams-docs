---
title: Combine bots with tabs
description: Describes how to use tabs and bots together
keywords: teams bots tabs development
ms.date: 05/15/2018
---
# Combine bots with tabs

Bots and tabs work well together, and are often combined. This section describes best practices and common patterns for using tabs and bots together.

## Associating user identities across bot and tab

For example:
Suppose your tab application uses a proprietary ID system to secure its content. Suppose you also have a bot that can interact with the user. Typically, you’ll want to show content in the tab that is specific to the viewing user. The challenge is that the user ID in your system is likely different from the Microsoft Teams user ID. So how do you associate these two identities?
In general, the recommended approach is to sign the user in with the bot using the same identity system used to provide authentication for the tab content. You can implement this via the sign-in action, which typically logs in the user via an OAuth flow.

This flow works best if your identity provider implements the OAuth 2.0 protocol. You can then associate the Teams user ID with the user’s credentials from your own identity service.

   ![Associating identities](~/assets/images/bots/associating_contexts)

Coming soon: our team roster API will provide basic user profile information, such as email address and Azure ActiveDirectory ObjectID, which is a unique user identifier that is commonly used across Microsoft Office365 applications. If your tab content is authenticated via Azure ActiveDirectory, you can use this value to associate user contexts between bot and tab interactions without requiring the user to sign into the bot.
Constructing deep links to tabs in messages from your bot
In many cases, you’ll want to show more content than can fit inside of a card, or provide a way to complete complex form-filling tasks.
To create the most seamless user experience, you’ll want to take advantage of the full Teams platform, which includes the tab canvas. For example, consider navigating the user to the tab when he or she clicks on the card from your bot. For this to happen, you’ll need to encode your bot’s message to include a deep link URL, either via markup or as the target of the openUrl action.
The format for constructing a deep link is documented here, and relies on providing an entityId, which is an opaque value that maps to a unique entity in your system. When the tab is created, you should ideally store some simple state (e.g. flag) on your backend indicating the tab has been created in the channel. Then, when your bot constructs a message, it can target the entityId associated with that tab.
Note: in 1:1 chats, because tabs are “static” and preinstalled, you can always assume its existence and thus construct deep links accordingly.
Sending notifications for tab updates
Often, you’ll want to notify the end user whenever an update or a user action occurs in a tab. An example scenario is assigning a task or ticket to a fellow team member and then notifying that team member.
There are two ways of achieving this scenario:
1)	If you wish to notify an entire channel, your bot can asynchronously post a message to the channel. Note that there is currently no way for a bot to proactively create the “tab conversation”

2)	If you wish to only notify the recipient or interested parties involved with the action, your bot can send a 1:1 chat message to the user. You should first check to see if a 1:1 conversation between your bot and the user exists. If not, you can call the CreateConversation API to initiate the 1:1 chat.
In both cases, use event notifications wisely and never spam the user with unnecessary updates!
Combining channel and private interactions with your bot
When interacting in a channel, your bot should be smart about taking certain conversations “offline” with a user. For instance, suppose a user is trying to coordinate a complex task, such as scheduling with a set of team members. Rather than have the entire sequence of interactions visible to the channel, consider sending a 1:1 chat message to the user. Your bot should be able to easily transition the user between 1:1 and channel conversations without losing state.
Note: don’t forget to update the channel when the interaction is complete to notify the other team members.
Embedding a button to add your app to Teams
If your app is in the Microsoft Teams store, you can generate a button that, when clicked, launches the Teams experience and shows a selection dialog for the user to add the app.
The format is as follows:  https://teams.microsoft.com/l/app/<appId>
Where appID is the GUID they declare in the submitted manifest.
Example: https://teams.microsoft.com/l/app/56E1A16C-DDB4-46C0-B4B1-FC634ED86DDD
// Todo: provide asset that developers can embed on their website
FAQ
Do you support tabs on mobile?
Microsoft Teams does not currently embed content on our mobile clients – we recommend that you supply a fallback URL in your tab definition such that we can load the appropriate responsive web content on mobile devices. 
How do I send ‘ephemeral messages’ to a user?
We don’t currently support the ability for a bot to message a user in a channel such only a specific user can view the message. The current workaround is to send a message to the user in 1:1 chat. 
