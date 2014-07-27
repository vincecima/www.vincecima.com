---
title: Introducing Nest
layout: post
---

I found that Twitter favorites(stars) are the best way to mark Tweets that I want to come back to later. I used to use IFTTT to have each favorited tweet emailed to me. However, the big API changes back in August forced IFTTT to shut down that particular tool. So I created Nest to restore some of that functionality.

Nest will email you each of your favorited tweets in a digest format and then un-favorite them. It is not appropriate to use if you want to preserve your favorites.

Below are instructions to get the script running on Heroku. It is completely self contained and doesn't require any other outside services.

1. Create Twitter App

	Sign into [dev.twitter.com](http://dev.twitter.com) and [create](https://dev.twitter.com/apps) a new application. Select the application you just created from the **My Applications** list, select the **OAuth tool** tab and take note of the values under **OAuth Settings**. You will need these 4 values later to setup your connection to the Twitter API.

2. Create Heroku App

	Sign up for Heroku (or use your existing account) and download the [Heroku Toolbelt](https://toolbelt.heroku.com/) for your platform. This will let you push a copy of the Nest application to your server. Don't create a new application just yet.

3. Clone Nest repo

	Use the git command line or your favorite GUI tool to clone the Nest [repo](https://github.com/vincecima/nest).

4. Create Your Heroku App

	From your copy of the Nest main folder, execute `heroku create` and then `git push heroku master` to push your copy of Nest to Heroku.

5. Install SENDGRID Add On

	Nest uses the SENDGRID email add on to send you your Twitter favorites. Just run `heroku addons:add sendgrid:starter` from your Nest folder to install it.

6. Set ENV variables

	Nest requires the setting of 5 environmental variables before it can function. From the command line in the Nest folder you run `heroku config:add NEST_CONSUMER_KEY=aaaaxxxxxbbbbb` to set the variable. Here are the variables:

	* NEST_CONSUMER_KEY (Found in the OAuth info discussed above)
	* NEST_CONSUMER_SECRET (Found in the OAuth info discussed above)
	* NEST_OAUTH_TOKEN (Found in the OAuth info discussed above)
	* NEST_OAUTH_SECRET (Found in the OAuth info disucssed above)
	* NEST_EMAIL (The email address where you want your favorites sent)

7. Execute a Test Run

	From the Nest folder, run `heroku run bin/nest`. If everything goes well, you should get an email in your inbox soon after with your 200 most recent favorites. Those same favorites will have been un-favorited as well.

8. Setup Cron Job

	Last step is get Heroku to execute the `nest` command for us everyday. Execute `heroku addons:add scheduler:standard` from the command line to enable this ability. Then login to the [Heroku Dashboard](https://dashboard.heroku.com/apps) and select the app you created. You should see **Heroku Scheduler Standard** in the list of **Add-ons**. Select it and you will be taken to the Scheduler interface. Pick **Add Job..** and set the command to run to `nest`. From there you can set your **frequency** and click **Save** to finish.