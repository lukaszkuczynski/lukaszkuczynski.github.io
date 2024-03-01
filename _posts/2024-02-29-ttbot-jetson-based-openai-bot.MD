---
layout: post
title: TTbot - Track&Tell Jetson based bot that connects to OpenAI
category: robotics
tags: robotics,ai,openai
# featured-img: AWS-DataAnalytics-Specialty-2020
---


# TTbot - bot that says truth

The following is my short story on upgrading my previous bot to be OpenAI-compatible.

With the recent surge of GenAI I decided not to be left behind, and to follow the stream. With the previous version I struggled with few AWS ML services (like Rekognition) but detection was limited to objects only. Now I am feeding Vision API on OpenAI with all pictures and ask questions on that. Here is small demo of what it can do:

<iframe width="560" height="315" src="https://www.youtube.com/embed/FWcyaegPZhs" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>



## 2 behaviours
Basically my TTbot has 2 main behaviours : tracking and telling. 

In *tracking* it uses onboard Jetson-optimized ML algorithms to follow the humans. As it is based on the one of the most famous ML object detection algorithms - SSD Mobilenet. It can detect multiple objects.  Unfortunately it has really low performance on small objects. So this is why I chose it will follow humans and it mostly follows me (thankfully I am big). 

In *tell*    behaviour it tries to answer the question asked based on what it sees ahead of it. This is pretty complicated behaviour so for this purpose I have designed State Machine that is performing several steps, as shown below

![](/assets/img/posts/ttbot.jpg)


## Lessons learned

During upgrade of my bot I learned few interesting lessons. Here I am shamelessly sharing them with you

### ROS makes sense
Before I started to design communication in TTbot I attempted to start learning ROS. But at some point I gave up as I felt it will be too much for a simple bot. So I successfully developed the communication mechanism based on MQTT - with pubsub and so on. Works good, but not always. Some messages are lost. I am also lacking ROS abstractions like actions (that can be pausable, retriable). It would be easier to have other ROS ecosystem tools like easy way of plugging other packages and using tools for monitoring and recording events (bags).

### No encoders - no control
Bot knows how to move forward. However this poor thing have no feedback on the real wheel turning speed. So it goes astray, a lot. Next time I attempt to work with the bot I need to have total feedback loop so I know exactly how fast we’re going.

### Don’t ignore hardware specs
For many weeks I have successfully tested servos and sensors working on non-default voltage under 5V. As I am using ESP32 they have default voltage levels of 3.3V. I just did not find it amusing to play with level translation or buy 5V compatible controllers. So I started to believe that powering those components with 3.3V is optimal. 
Then for some time I was using my LMN298 output 5 V to power servos and sensors. But it is not efficient - current was too low. It became clear when servos sometimes acted weirdly and sensors were stuck. All problems just disappeared when I switched everything to 5 V line directly from a powerbank that gives steady 5V power and current as expected.
I also noticed Jetson Nano works much better when I am using 3A output of powerbank (as opposed to 2A I used earlier). 
So following specs makes sense. And I learned it hard way.

## Way forward
In the long run I can see following improvements and fixes of the current platform:
- migrate to ROS
- enable cruise control with encoder

Generally speaking I am happy I have tried to revive the project in the current shape, cause TTbot moved me to re-start learning ROS. And it really helped me to see that OpenAI is powerful platform. It is cool ti can speak Polish, too 🙂


