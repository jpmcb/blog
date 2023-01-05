---
title: "Slack Is Always Watching ..."
date: 2019-01-21T21:18:06-07:00
draft: false
---

(Note: this is from a blog archieve dated 2019/01/21. These opinions are my own and the slack API may have changed)
TLDR: The Slack API exposes endpoints for a token holder to read all public and private messages.  

In today's world, violations of privacy are no surprise. Between all the leaks and data dumps, many people have accepted this as "just the world we live in". But what if information was exposed that could be used to judge your work performance? Or steal your company’s intellectual property? 

In this post, I will show how a Slack app could potentially leverage the Slack API to snoop on all public and private messages in a Slack workspace.

## The veil of privacy

A slack private message is not truly private. It is only hidden behind a thin veil of secrecy. With a workspace API token in hand, someone could lift that veil and see all. 

Here, with a short example, we will show how easily that can be done. 

First, the workspace owner or admin (depending on permissions), must access the [Slack API website](https://api.slack.com/). There, they can build an app or give third party permissions to install a “marketplace” app. This is fairly straight forward and exposes several workspace tokens for the app to use. These are secret tokens, so they will be omitted in this example. 

Next, the app builder must enable the ["message" workspace event.](https://api.slack.com/events/message) If I was a nefarious third-party app builder, I would simply request various permissions related to channel, im, or group "history". For a full list of events and their permission scope, see [this list.](https://api.slack.com/docs/oauth-scopes)

Next, if building the app, an endpoint must be designated for the API to send the event payload. This event triggers whenever a message is sent in a direct message channel or fulfills the event conditions. For a full description of the Slack API event loop, [check this out.](https://api.slack.com/events-api#the_event_loop)

Now that we have the API set up to send event payloads, lets build a small Node Express app with an endpoint to receive the event json.

```javascript
// Events API endpoint
app.post('/events', (req, res)  => {
    switch (req.body.type) {
        case 'url_verification': {
                // verifies events API endpoint 
                res.send({ challenge: req.body.challenge });
            break;
        }

        case 'event_callback': {

            // Respond immediately to prevent a timeout error
            res.sendStatus(200);
            const event = req.body.event;

            // Print the message contents
            if(event.type == 'message') {
                console.log('User: ', event.user);
                console.log('Text: ', event.text);

                // Do other nefarious things with events json
            }
        }
    }
})
```

In this short node express endpoint, we can respond with the event token (necessary for verifying the app when challenged) and snoop on private messages. Let’s use [ngork](https://ngrok.com/), start the express app, and send a private message: 

```
App listening on port 8080!
User:  UBB22VCKC
Text:  hello world
```

We can see that my Slack user ID is exposed and the message I sent is exposed. At this point, the app could do anything with this information.

This not only applies to single channel DMs, but the Slack API exposes several event subscriptions for message events in specific channels, specific groups, multiparty direct messages, private channels, and even every message sent in a workspace. The app builder simply must turn on these events request the appropriate permissions and the payload will be sent to the designated endpoint. 

In short, it requires very little configuration and code to access and expose private Slack messages. 

## What can you do? 

1. Be extremely mindful of the apps and permissions you give third party apps. Ask yourself basic questions about these permissions. If you are installing a fun GIF app, why dose it requires channel history permissions?

2. Use Slack apps that have been made open source. Don't hesitate to poke around a repository if you are questioning why an app requires certain permissions!

3. Request that any custom apps your Slack workspace uses are made open source. 

---

If you found this blog post valuable,
consider [subscribing to future posts via RSS](https://johncodes.com/index.xml)
or [buying me a coffee via GitHub sponsors.](https://github.com/sponsors/jpmcb)

---

{{< comments >}}
