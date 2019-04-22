
# ringcentral-chatbot-template-python <!-- omit in toc -->

Template to build RingCentral chatbot with [ringcentral-chatbot-python](https://github.com/zxdong262/ringcentral-chatbot-python).

## Table of contents <!-- omit in toc -->

- [Prerequisites](#prerequisites)
- [Development & Quick start](#development--quick-start)
- [Test bot](#test-bot)
- [Create your own bot logic](#create-your-own-bot-logic)
- [Advanced topics](#advanced-topics)
- [License](#license)

## Prerequisites

- Python3.6+ and Pip3
- Create the bot App: Login to [developer.ringcentral.com](https://developer.ringcentral.com) and create an `public` `Server/Bot` app with permissions: `ReadAccounts, Edit Extensions, WebhookSubscriptions, Glip`(or more as you may need), ---> <a href="https://developer.ringcentral.com/new-app?name=Sample+Bot+App&desc=A+sample+app+created+in+conjunction+with+the+python+bot+framework&public=true&type=ServerBot&carriers=7710,7310,3420&permissions=ReadAccounts,EditExtensions,SubscriptionWebhook,Glip&redirectUri=" target="_blank">Click to Create app</a>

## Development & Quick start

```bash

# init
bin/init
source ./venv/bin/activate

# run ngrok proxy
# since bot need https server,
# so we need a https proxy for ringcentral to visit our local server
./bin/proxy
# will show:
# Forwarding https://xxxxx.ngrok.io -> localhost:9890

# create env file
# .env already created from .sample.env
# just edit .env, set proper setting,
RINGCENTRAL_BOT_SERVER=https://xxxxx.ngrok.io

## for bots auth required, get them from your ringcentral app page
RINGCENTRAL_BOT_CLIENT_ID=
RINGCENTRAL_BOT_CLIENT_SECRET=

# and goto your ringcentral app setting page, set OAuth Redirect URI to https://https://xxxxx.ngrok.io/bot-oauth

# run local dev server
# rcs is cli server module from ringcentral_chatbot_server
rcs bot.py
```

## Test bot

- Goto your ringcentral app's bot section, click 'Add to glip'
- Login to [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com), find the bot by searching its name. Talk to the bot.
- Edit config.py to change bot bahavior and test in [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com)

## Create your own bot logic

- You can edit/add method in `bot.py` you need to use, write your own bot logic, restart the app, check it in [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com).
- You can read all configs available from [sample-bots/kitchen-sync.py](https://github.com/zxdong262/ringcentral-chatbot-python/blob/master/sample-bots/kitchen-sync.py)

And we have examples bots you can check out as examples:

- [date-time-chatbot](https://github.com/zxdong262/ringcentral-date-time-chatbot) : Simple ringcentral chatbot which can tell time/date.
- [assistant-bot](https://github.com/zxdong262/ringcentral-assistant-bot) : Simple assistant Glip bot to show user/company information, this bot will show you how to access user data.
- [poll-bot](https://github.com/zxdong262/ringcentral-poll-bot) : Example poll bot, this bot will show you how to create/use custom database wrapper.

## Advanced topics

- [Deploy to AWS Lambda](https://github.com/zxdong262/ringcentral-chatbot-python/blob/master/docs/deploy-to-aws-lambda.md)
- [Use or write extensions](https://github.com/zxdong262/ringcentral-chatbot-python/blob/master/docs/extensions.md)

## License

MIT