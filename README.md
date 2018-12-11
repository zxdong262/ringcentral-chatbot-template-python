
# ringcentral-chatbot-template-python <!-- omit in toc -->

Template to build RingCentral chatbot with [ringcentral-chatbot-python](https://github.com/zxdong262/ringcentral-chatbot-python).

## Table of contents <!-- omit in toc -->

- [Prerequisites](#prerequisites)
- [Development & Quick start](#development--quick-start)
- [Test bot](#test-bot)
- [Create your own bot logic](#create-your-own-bot-logic)
- [Building and Deploying to AWS Lambda](#building-and-deploying-to-aws-lambda)
- [Use Extensions](#use-extensions)
- [Write a extension your self](#write-a-extension-your-self)
- [License](#license)

## Prerequisites

- Python3.6+ and Pip3
- Create the bot App: Login to [developer.ringcentral.com](https://developer.ringcentral.com) and create an `public` `Server/Bot` app with permissions: `ReadContacts, ReadMessages, ReadPresence, Contacts, ReadAccounts, SMS, InternalMessages, ReadCallLog, ReadCallRecording, WebhookSubscriptions, Glip`

## Development & Quick start

```bash

# use virtualenv
pip3 install virtualenv # might need sudo

# init virtual env
virtualenv venv --python=python3

# use env
source ./venv/bin/activate

# install required modules
pip install -r requirements.txt

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
./bin/start
```

## Test bot

- Goto your ringcentral app's bot section, click 'Add to glip'
- Login to [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com), find the bot by searching its name. Talk to the bot.
- Edit config.py to change bot bahavior and test in [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com)

## Create your own bot logic

```bash
# create custom bot config file
cp config.sample.py config.py
```

Then just uncomment the method in `config.py` you need to use, write your own bot logic, restart the app, check it in [https://glip-app.devtest.ringcentral.com](https://glip-app.devtest.ringcentral.com).

And we have examples bots you can check out as examples:

- [date-time-chatbot](https://github.com/zxdong262/ringcentral-date-time-chatbot) : Simple ringcentral chatbot which can tell time/date.
- [assistant-bot](https://github.com/zxdong262/ringcentral-assistant-bot) : Simple assistant Glip bot to show user/company information, this bot will show you how to access user data.
- [survey-bot](https://github.com/zxdong262/ringcentral-survey-bot) : Example survey bot, this bot will show you how to create/use custom database wrapper.

## Building and Deploying to AWS Lambda

AWS Lambda with API Gateway and DynamoDB would give us a flexible way to deploy the bot.

*Be aware that AWS Lambda **ONLY works in linux** on an x64 architecture. For **non-linux os**, we need **docker** to build dependencies, should [install docker](https://docs.docker.com/docker-for-mac/) first.

Get an AWS account, create `aws_access_key_id` and `aws_secret_access_key` and place them in `~/.aws/credentials`, like this:

```bash
[default]
aws_access_key_id = <your aws_access_key_id>
aws_secret_access_key = <your aws_secret_access_key>
```

For more information, refer to [https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html](https://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html)

```bash

# install serverless
npm i

# create serverless.yml
cp dev/lambda/serverless.sample.yml dev/lambda/serverless.yml
```

Edit `dev/lambda/serverless.yml`, and make sure you set the proper name and required env.

```yml
# you can define service wide environment variables here
  environment:
    ENV: production
    # ringcentral apps

    ## for bots auth, required
    RINGCENTRAL_BOT_CLIENT_ID:
    RINGCENTRAL_BOT_CLIENT_SECRET:

    ## for user auth, could be empty if do not need user auth
    RINGCENTRAL_USER_CLIENT_ID:
    RINGCENTRAL_USER_CLIENT_SECRET:

    ## common
    RINGCENTRAL_SERVER: https://platform.devtest.ringcentral.com
    RINGCENTRAL_BOT_SERVER: https://xxxxx.execute-api.us-east-1.amazonaws.com/dev

    # db
    DB_TYPE: dynamodb
    DYNAMODB_TABLE_PREFIX: ringcentral-bot
    DYNAMODB_REGION: us-east-1
    DYNAMODB_ReadCapacityUnits: 1
    DYNAMODB_WriteCapacityUnits: 1

```

Deploy to AWS Lambda with `bin/deploy`

```bash
# Run this cmd to deploy to AWS Lambda
bin/deploy
```

After successful deploy, you will get the https api url:

```bash
Service Information
service: ringcentral-bot
stage: dev
region: us-east-1
stack: ringcentral-bot-dev
api keys:
  None
endpoints:
  ANY - https://dddddd.execute-api.us-east-1.amazonaws.com/dev/{action+}
  GET - https://dddddd.execute-api.us-east-1.amazonaws.com/dev/
```

Relpace `RINGCENTRAL_BOT_SERVER: https://xxxxx.execute-api.us-east-1.amazonaws.com/dev` in serverless.yml with
`RINGCENTRAL_BOT_SERVER: https://dddddd.execute-api.us-east-1.amazonaws.com/dev`
 and run `bin/deploy` to deploy again.

Watch Lambda server log by run:

```bash
bin/watch
```

Do not forget to set your RingCentral app's redirect URL to Lambda's API Gateway URL, `https://dddddd.execute-api.us-east-1.amazonaws.com/dev/bot-oauth` for bot app.

## Use Extensions

RingCentral Chatbot Framework for Python Extensions will extend bot command support with simple setting in `.env`.

Just set like this in `.env`

```bash
EXTENSIONS=ringcentral_bot_framework_extension_botinfo,ringcentral_bot_framework_extension_some_other_extension
```

And install these exetnsions by `pip install ringcentral_bot_framework_extension_botinfo ringcentral_bot_framework_extension_some_other_extension`, it is done.

![ ](https://github.com/zxdong262/ringcentral-chatbot-python-ext-bot-info/raw/master/screenshots/ss.png)

You can search for more extension in [pypi.org](https://pypi.org) with keyword `ringcentral_bot_framework_extension`.

## Write a extension your self

Write one extension will be simple, just check out [botinfo extension](https://github.com/zxdong262/ringcentral-chatbot-python-ext-bot-info) as an example, you just need to write one function there.

```python
# botinfo extension's source code
# https://github.com/zxdong262/ringcentral-chatbot-python-ext-bot-info/blob/master/ringcentral_bot_framework_extension_botinfo/__init__.py
import json

name = 'ringcentral_bot_framework_extension_botinfo'

def botGotPostAddAction(
  bot,
  groupId,
  creatorId,
  user,
  text,
  dbAction
):
  """
  bot got group chat message: text
  bot extension could send some response
  return True when bot send message, otherwise return False
  """
  if not f'![:Person]({bot.id})' in text:
    return False

  if 'bot info' in text:
    botInfo = bot.platform.get('/account/~/extension/~')
    txt = json.loads(botInfo.text())
    txt = json.dumps(txt, indent=2)
    msg = f'![:Person]({creatorId}) bot info json is:\n' + txt

    bot.sendMessage(
      groupId,
      {
        'text': msg
      }
    )
    return True
  else:
    return False
```

## License

MIT