---
layout: post
title: Access Reddit & Send an SMS using Python
subtitle: 
---

## Access Reddit & Send an SMS using Python

I recently discovered the [Python Reddit API Wrapper (PRAW)](http://praw.readthedocs.io/en/latest/index.html) which provides an easy way to communicate with Reddit's API within your program.

The idea here was fairly simple, I wanted to obtain the top submission for a subreddit and have it sent to my phone, in this case I'm using /r/jokes.  This required two steps: authenticating with Reddit and authenticating with an sms routing service.  The one that I ended up using for this was a service called Twilio.  For the purposes of this experiment I just signed up with a trial account & phone number on Twilio's site. 

The PRAW documentation was fairly straight forward and it didn't take long to get going at all.

You will need to install the PRAW & Twilio modules in terminal  (or cmd line if you're on pc)

You will need to have your own number to be able to verify it with Twilio.  You will also be assigned a number through their service, which you'll use as your *from* number in the below example.

My code is pretty straight forward but here's what's going on:

1. Import the PRAW & Twilio Modules
2. We set our individual reddit client info to a variable. Make sure to set your user_agent to something useful, preferably the name of your application with your username, just don't include the word 'bot'
3. I set a results variable and I set the limit = 1 (theoretically you could send many lines)
4. I initiate a simple for loop that iterates over the submissions for the subreddit, and to make it look a little better I added a new line. Note that the submission text (not title) in PRAW is called 'selftext'
5. Set the proper credentails for Twilio, set the To and From numbers accordingly 
6. Run the print command on our created message, this will send it.

From now on all you should need to do is run this  file

```Python
import praw
import twilio
from twilio.rest import Client

reddit = praw.Reddit(client_id="your ID here",
                     client_secret="your secret here",
                     user_agent = "Joke Grab by /u/yourusername")

results = ""
for submission in reddit.subreddit("Jokes").hot(limit=1):
    results+=str(submission.title + '\n' + submission.selftext)
    print(results)
    break
                                            
# send the appended message to my phone

#  Account SID from twilio.com/console
account_sid = "twilio account sid"
#  Auth Token from twilio.com/console
auth_token  = "twilio auth token"

client = Client(account_sid, auth_token)

message = client.messages.create(
    to="+9999999999", 
    from_="+9999999999",
    body=results)

print(message.sid)
```

## Additional thoughts
We can see how the PRAW module might have some pretty nifty applications.  Something else you could do with this is host it somehwere and automate the execution of your .py file, and have it send on a schedule.  You could also automate your own Twitter bot that posts submissions, or email submissions to yourself for that matter.  I hope to play around with it some more in the future!
