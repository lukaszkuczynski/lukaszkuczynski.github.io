---
layout: post
category: azure
tags: [cloud,azure,functions,serverless]
title: Using Azure to get notified about the weather
---

## Why project, why platform
The project called "notifier" stayed always in my mind.  It even had Python *incarnation* as you can check [on these repo](https://github.com/lukaszkuczynski/scout).  The problem was it was always to be very **generic** so I was under the flood of configuration requirements that were set by myself.  The platform of **Azure** is the accident in this regard, I have the access to it at my workplace, so even having some AWS background I decided to give it a try.

## Idea, building blocks
So the main idea is : to be notified when something changes.  So, f.e. when the weather changes (checking using API) I want to be notified about changes via some notification mechanism, say: Slack.  Using Azure Function Apps you are encouraged to decouple responsibilities into different functions, following the "microservice" way of thinking.
There are following building blocks that will enable it using Function apps:
 - read data once an hour
 - write the reading to the db (CosmosDB is the default choice)
 - trigger the message on a queue
 - compare the reading with the previous value
 - if the current value is different than the previous one, write it to *changes* collection
 - notify me about the change

### how
`Azure functions` allow the designer to use the following features I will extensively use here to build a pipeline: bindings, triggers and outputs.

### trigger
Trigger is something that can start the execution of a function.  It can be:
 - time  (cron expression)
 - new message on a queue
 - new db record / document
 - new file created
There are more, check it [here](https://docs.microsoft.com/en-US/azure/azure-functions/functions-triggers-bindings#supported-bindings).

### binding
When a function is executed, you do not need to worry how to get right data from Azure infrastracture, because the platform is able to **inject** the right value into the function as its parameter.  It can be easily designed using *Azure portal* but after you will feel more professional you can handle functions json to create and modify its bindings. 

### output
Function produces a result in its mathematical sense.  This can be done in the Azure cloud, however we have option of having 0 or more actions as the result of a function execution.  So, for instance, we can say that if a message goes to a queue there should be a document created in a CosmosDB collection.

## The pipeline
These are building blocks that made very simple notification mechanism possible. I sacrificed only few hours to make it so maybe results are not so big, but I can taste some possibilities of a platform.

### Fetch data : Timer -> Queue
First of all I need the data. It comes [from api served by openweathermap portal](https://openweathermap.org/price), that anyone can use for developer purposes for free.  Basically I fetch data as json for my city (Wrocław, Poland) and then put it on a queue.  The data as for this example is very simple: it is just a short description of a weather: rain, cloudy, clear.  When it is read, I put it into a queue, it is simple Azure Queue storage as described [here](https://docs.microsoft.com/en-US/azure/azure-functions/functions-create-storage-queue-triggered-function).
```csharp
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;


public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputQueueWeather)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    string url = "https://api.openweathermap.org/data/2.5/weather?q=Wroclaw,pl&appid=<MYKEY>";
    var client = new HttpClient();
    var response = client.GetAsync(url).Result;
    var content = response.Content;
    string responseString = content.ReadAsStringAsync().Result;
    log.Info(responseString);
    var weather = JsonConvert.DeserializeObject<WeatherResponse>(responseString as string);
    string mainWeatherInWroclaw = weather.weather[0].main;
    log.Info(mainWeatherInWroclaw);
    outputQueueWeather.Add(mainWeatherInWroclaw);
}

class Coord {
    public double lat { get; set; }
    public double lon { get; set; }
}

class Weather {
    public int id {get; set;}
    public string main  {get; set;}
    public string description {get; set;}
    public string icon {get; set;}
}

class WeatherResponse
{
    public Coord coord { get; set; }
    public IList<Weather> weather { get; set; }
}
```

### Save the data that has been read, Queue -> DB
When the data is put on a queue I put in onto a collection stored in the persistence.  It makes the comparison possible so to decide if the change occured or not.  
```csharp
using System;

public static void Run(string weatherQueueItem, TraceWriter log, ICollector<WeatherItem> outputDbDocument)
{
    log.Info("Executing with "+weatherQueueItem);
    var dbWeatherItem = new WeatherItem(weatherQueueItem);
    outputDbDocument.Add(dbWeatherItem);
}

public class WeatherItem
{
    public string Text { get; set; }
    public WeatherItem(string text)
    {
        Text = text;
    }

}
```

### Data comparison, DB -> Queue
After inserting a document into CosmosDB collection it triggers a calculation.  It means we are just taking two previous documents sorted by timestamp with descending order.  This is **not** visible in the listing below, as this "query" is enclosed in function definition (json file). When a comparison is done we know if there was a change.  Information about this change is propagated to messaging system again, of course different queue is used.
```csharp
#r "Microsoft.Azure.Documents.Client"
using System;
using System.Collections.Generic;
using Microsoft.Azure.Documents;


public class WeatherItem {
    public string Text {get; set;}
}

public static void Run(IReadOnlyList<Document> documents, IEnumerable<WeatherItem> lastWeathers, TraceWriter log, ICollector<string> outputChangedWeather)
{
    if (lastWeathers != null && lastWeathers.Count() > 0)
    {
        
        log.Info("Documents count " + lastWeathers.Count());
        if (lastWeathers.Count() >= 2) 
        {
            var enumerator = lastWeathers.GetEnumerator();
            enumerator.MoveNext();
            var firstValue = enumerator.Current.Text;
            enumerator.MoveNext();
            var secondValue = enumerator.Current.Text;
            if (firstValue != secondValue) 
            {
                string changeText = "change! "+firstValue +" > " + secondValue;
                log.Info(changeText);
                outputChangedWeather.Add(changeText);

            } 
            else 
            {
                log.Info("Values the same."+firstValue);
            }
        }
    }
    
}
```

### Change detected, take an action! Queue -> DB, Email
Finally when a change comes from Azure Queue, we can do something about it. I will save it to DB (for historical purposes) and notify myself about it.  There are plenty of possibilities when notification is concerned.  I use simple Email notification provided by SendGrid binding.  First you have to use your SendGrid API key which is free for developer usage quotas, the proof is [as follows](https://openweathermap.org/price).  Then having that key you can just use it as a SendGrid binding as shown in following example
```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(string myQueueItem, TraceWriter log, out Mail mail)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    mail = new Mail
    {        
        Subject = "Azure news",
        From = new Email("lukaszazurowy@azure.com")        
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("YOUR_EMAIL_GOES_HERE"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = myQueueItem
    };
    mail.AddContent(content);
    mail.AddPersonalization(personalization);
    
}    
```

## Summary
I learned a lot from Pluralsight about Azure. Using Azure to meet my "business requirements" was cool, because I realized, that it is:
* relatively easy 
* well documented
* enables "short time to market"
* cheap, because running function apps is nearly 0USD per run

I can see future improvements, however: 
* data is being propagated between functions as a string, I should consider type and object shared between functions
* I have to think if connections are well designed (f.e. if I should trigger the notification of a change after DB document insertion or directly from a queue system).


