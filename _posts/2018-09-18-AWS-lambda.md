---
layout: post
title: Monitoring Wrocław weather with AWS
category: aws
tags: aws,lambda,weather,cloud,serverless,SQS
featured-img: lambda
---

What if I wanted to compare AWS Lambdas with Azure Function apps?  The previous weeks I did some Azure sample app.  [There is some article on that](https://lukaszkuczynski.github.io/azure/2018/08/20/Azure-Notifications.html).  But I always felt AWS compared to Azure is smarter, GUI is lighter and I love Python.  So why not to copy my project to AWS?

## Plan
Create a notification mechanism telling me about changes of weather in Wrocław.  This will be done using Lambda architecture in AWS mechanism.

## Implementation
### Check the weather
Lambda function can be fired by *CloudWatch Events*, using which I can set up some cron expression.  Thus I will check weather exposed by [openweather API](https://openweathermap.org/current) and put it on a queue.  Queues in Amazon are part of so-called *SQS*.  Handling SQS in Amazon is pretty easy using [Python boto clients](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/sqs.html). Following there is my function to fetch data.  In my case every 10 minutes I will call *openweather* API to query the current weather and I will put its description in a queue.

```python
import os
from datetime import datetime
from urllib.request import Request, urlopen
import json
import boto3

SITE = os.environ['site']  # URL of the site to check, stored in the site environment variable


def validate(res):
    return EXPECTED in res


def lambda_handler(event, context):
    print('Checking {} at {}...'.format(SITE, event['time']))
    try:
        req = Request(SITE, headers={'User-Agent': 'AWS Lambda'})
        response = urlopen(req).read()
        print(response)
        data = json.loads(response.decode("utf-8"))
        weather = data['weather'][0]['main']
        dt = data['dt']
        dt_iso = datetime.isoformat(datetime.fromtimestamp(dt))
        print(f"weather is {weather} with UTC time {dt_iso}")

        queueUrl = os.environ['SQS_NEW_WEATHER']
        client = boto3.client('sqs')
        response = client.send_message(
            QueueUrl=queueUrl,
            MessageBody=weather
        )

    except:
        print('Check failed!')
        raise
    else:
        print('Check passed!')
        return weather
    finally:
        print('Check complete at {}'.format(str(datetime.now())))
```

### Write to persistence
When received the value I would like to compare it against the previous one to decide whether there was a change.  I have to put **state** somewhere.  I will use default database for AWS: DynamoDB.

The function is triggered for a new message on SQS, and it is easily configurable with AWS lambda GUI.  The message received on SQS is available as *event* parameter of lambda.

```python
import boto3
from boto3.dynamodb.conditions import Key
from datetime import datetime


def lambda_handler(event, context):
    print("received!")
    print(event)
    new_value = event['Records'][0]['body']
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('weather')
    table.put_item(
       Item={
            'created_at': datetime.utcnow().strftime('%Y%m%d_%H%M%S'),
            'type': 'wroclawWeather',
            'text': new_value
        }
    )
    return "time created with value "+new_value
```
So the above function will create a new document in DynamoDB instance.  As you can see *boto3* library helps to handle DynamoDB connections. All you need is to call *resource* and *table* and you are ready to `put_item` into the table.  This will enable the comparison in the near future. Behold!

### Compare time!
Having the new document inserted into the table, I can compare it against the previous one of the same type.  I will use again the appropriate trigger, this time it will be "New row" trigger for a DynamoDB.  The last 2 items will be fetched using this complicated query with *KeyConditionExpression* because I want to get only two previous values of the exact type (like a `WHERE` clause in SQL).

```python
import os
import json
import boto3
from boto3.dynamodb.conditions import Key

def lambda_handler(event, context):
    # print("Received event: " + json.dumps(event, indent=2))
    records = event['Records']
    if len(records) != 1:
        print(f"len records != 1, len = {len(records)}")
        print(records)
        return "len(REC) <> 0"
    record = records[0]
    current_type = record['dynamodb']['NewImage']['type']['S']
    print(f"current_type = {current_type}")
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('weather')
    response = table.query(
        KeyConditionExpression=Key('type').eq(current_type),
        ScanIndexForward=False, 
        Limit=2
    )
    new_value = record['dynamodb']['NewImage']['text']['S']
    old_value = response['Items'][1]['text']
    
    if old_value != new_value:
        change_text = "changed IN DYNAMO trigger! "+old_value+"->"+new_value
        print(change_text)
        queueUrl = os.environ['SQS_CHANGED_WEATHER']
        client = boto3.client('sqs')
        response = client.send_message(
            QueueUrl=queueUrl,
            MessageBody=change_text
        )
    print(f"Current FROM DYNAMO {new_value}, previous {old_value}")
```
If the value was changed the message will wander to another SQS topic, and this one I will use to notify the user about the changes.

### Notification with SNS
When the weather is changed, it will go to **Simple Notification Service**, that can be used to... apply notification rules.  In my case, I will simply use an email to be sent with the text of a change.

And once again, thanks to *boto3* library enables I create a SNS client so I can send SNS message super-easily from within the lambda.
```python
import os
import json
import boto3


def lambda_handler(event, context):
    print("called weatherChanged")
    print(event)
    change_text_value = event['Records'][0]['body']
    print("I received a change: "+change_text_value)
    arn = os.environ['SNS_TOPIC']
    message = change_text_value
    client = boto3.client('sns')
    response = client.publish(
        TargetArn=arn,
        Message=message
    )
    return change_text_value
```
After that one I just needed to configure my SNS connection.

### Configuring SNS
To receive emails I had to create a topic.  This topic has SNS address that I used in lambda function code.  Then there is a need to create a subscription.  You can use subscription of "Email" type, so then AWS will push the messages to your email inbox.

## AWS or Azure?
I configure the same business logic in both AWS and Azure.  Time for a comparison. 

### Azure pros:
*security made easier*

I wasn't forced to care about securty too much, all the functions were just connected to each other out-of-the-box.

*functions bundled together*

For a reason Function App in Azure is a root for all the functions inside, I could nicely put all the related functions in one resource.

### AWS pros:
*python*

The language is waaay better for me. Have no experience in C# and Python is just beautiful for playing with functions.

*no hassle with param bindings*

Binding in Azure is not so straighforward for me as AWS triggers and handling params for AWS Lambdas.  Just better.

*boto3* 

I had to make some strange returning and AsyncCollectors in Azure while AWS gives *boto3* that makes everything just simple.

My choice?
**AWS**.



