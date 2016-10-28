# Demo Script
===

The following script walks you through demonstrating the various aspects of APIC. Because of the time it takes to deploy to BlueMix and have APIC up and running, we will use a shared account with an APIC server already up and running. You should ensure that the API you build follows the script since it will match what is deployed to the running server.

Also ensure you have a valid Bluemix account.

## Table of Contents
---
[What is APIC?](###What is APIC?)

###What is APIC?
---

(*Intro to APIC, feel free to ignore/change/etc.*)

At a high level, APIC is a gateway for your APIs. It handles creating your API. It handles running your API. It handles managing access to your APIs, doing things like requiring you to sign up first and use a key. It handles checking that key and rate limiting it. It handles analytics for your API. And more. The important thing is that *your* API stays nice and simple and APIC handles the management.

APIC supports Node and Java-based APIs and has a generous free tier.

### Installation
---

(*Obviously not done live, but useful to let folks we use npm!*)

Just do: `npm install -g apiconnect`

This installs the toolkit where you can create your APIs, set up access, etc. You then deploy this to either an APIC server on prem or one running on IBM Bluemix. 

Remember that APIC works with Java. If you aren't a Node person, that's ok. But learn Node. Seriously.

You can then test the CLI by typing: `apic`

### Create
---

We're going to begin by creating a LoopBack application with the CLI.

    apic loopback

You may be asked to accept a license file: 

~~~~
➔ apic loopback
? Please review the license for API Connect available in $PATH and select yes to accept. (Use arrow keys)
❯ yes 
  no 
~~~~     

Where $PATH is the directory you were in when you installed apiconnect. Please accept this license to continue. If you do not see the license prompt, or do not see the license file, you can find it in the apiconnect folder where your node modules are installed (try npm list -g to see where they are installed).    

For the name, use the date and hour to ensure you get a good unique name: `june79am` for ex.

Select "notes" so you get an IMD db and an API (Notes) out of the box.

Change into the directory.

Start up the toolkit: `apic edit`

In the browser, login to Bluemix (and cross your fingers). Note the URL (should be 127.0.0.1:9000) as sometimes when you login to BM it doesn't bring you back to where you were.

Note - I think the designer remembers your last tab, so you may not start off in the best place. Click `APIs` if not selected.

Click the name of your APIs (which will be the same as your app name).

What you see here is a default set of APIs we got because we chose a sample application at the CLI. 

Click `/Notes`

As an example, here we can see a path, slash Notes, and various HTTP methods associated with working with Notes. (Scroll down to GET) That includes, obviously, fetching a list of notes. All of these various paths and operations come out of the box. Let's look at how this is defined.

Click `All APIs` and then `Models`. 

When you create APIs with APIC, you're using a model-centric framework called LoopBack. That's an open source Node.js framework designed for easy API creation. LoopBack uses a simple "model" system where you describe your data and it does all the rest.

Click `Note`

This is the Note model. It's got 2 properties, `content` and `title`. We can define a type, whether or not it is required, and other bits of metadata. The point is - from this simple description we got all of those various API end points out of the box. Let's build a new mode.

Click `All Models`
Click `Add`

We're going to call our model "Cat". 

And we'll give it a few properties:
    
    name (string, required)
    breed (string, required)
    age (string, required)

(Use required as it makes testing a bit easier later.) 

To be clear, these properties, their types, them being required, is arbitrary. Whatever your business needs are, you can just define what makes sense. And everything I've done here in this pretty web UI can also be done at the CLI as well. Use whatever you're most comfortable with.

Click the floppy disk icon to save since apparently we're still using that icon even though our kids have no idea what a floppy disk is.

(Aside: If you want to demo via CLI, use: `apic create --type model`)

Click back to `All APIs` and into your API set and point out the Cat stuff. Again, this is *all* automatic. I've got end points to create, read, update, and delete cats. Done. Now obviously in the real world I may want to tweak this. Maybe I don't want you deleting cats. I have full control over all of this and can tweak it to my heart's content.

(Optional stuff before going into Run:)
In case your curious, our data can be persisted in any number of storage systems (MySql, Oracle, Cloudant, Mongo, etc). LoopBack, and APIC, have a real basic ORM built in to handle persistence. Out of the box, it uses a RAM-based system. This is great for prototyping as you don't have to set up anything yet.

### Run
---

So let's talk about running and testing this. First thing I'll do is actually start the server. Again, I can do this from the command line, and it even told me what to do (`apic start`), but I can click the little play button on the bottom here too.

Click play. Give it a moment. Click on the "Application" url in the bottom bar (it'll be something like 'http://127.0.0.1:4001/', and will open up a new browser tab.

This is the API server we created. It's a LoopBack app and all your seeing here (it's a timestamp) is a default home page showing when the app started and how long it's been up. (You should see something like "{"started":"2016-10-28T20:39:56.379Z","uptime":202.198}".) You can modify that later of course. If I wanted to, I could test my APIs by typing them in here. But instead, we'll use the APIC Designer instead. 

Go back to the APIC tab and click Explore. What you have is an interactive testing tool for *all* of your APIs. Every API lets you test it, helps create sample data to use with your testing, and even shows you a CURL command if you want to test outside of the web page.

Scroll to `POST /Cats`

So here is how we make a new cat. This is a standard REST-based API so we do that by sending a POST request to Cats with our data. 

Scroll down to Generate and click it. This is random data based on the cat model we defined. (It isn't obvious, but you can click to edit.) Go ahead and change the name. You must name your cat Mr. Wafflepants. It's crucial.

Click `Call operation`


*** VERY IMPORTANT NOTE ***

One of two things will happen. If you see a good result, awesome. Move on. If you see: `Code: -1` and some BS text about a certificate issue, yeah, you have to do a one time operation to tell your browser to accept the temp cert. Click the link it tells you to. Depending on your browser, you will need to tell it to go on and run the URL even though it is bad. You then get a new error: `PreFlowError`. That's actually good. Go back to the tab, and try it again. The good news is that now you may get another bug. If you run `Call operation` again and see this: https://$(catalog.host)/api/Cats, then, well, you're screwed. Try restarting the app.  Try reloading the web page (maybe hold shift too). Good luck!

Note - if you forgot to start the server, that will *definitely* cause the ${catalog.host} issue. Start it. Then randomly click around for a few clicks, then come back to Explore. Again, cross your fingers.

*** END IMPORTANT NOTE ***

Now go down to the `GET` operation and run that. You should see the new cat you created.

Remind people though that I can use my own tools to test these APIs, like Postman for example.

If you want to test your API outside of the web UI, be sure you use the *Application* URL and not *Micro Gateway*. Basically test with http. 

### Manage
---

So outside of creating and running APIs, you can also manage your APIs too. Back in designer, you'll notice a Products tab. A "Product" in this context is simply a collection of APIs. You could imagine having 5 APIs but you only want to release 4 in your initial launch. A Product lets you gather those 4 up and release them as one entity. 

You'll see have one product already, named the same as the application. Click on it. 

You'll see various metadata fields (point out Version and Description as possibly interesting things to set). 

Then go to Plans. This is where things get cool. First off, automatically, and you'll see this in a bit, developers will need to subscribe to the product in order to use my APIs. I'll give them an key and APIC will handle validating that key for me. I don't write that code. It's great. Here's where I can tweak the limit. Right now it is 100 per hour. 

You can go back to Explore, click on GET /Cats, and call the operation, and point out how it passes header info back that matches the rate limit. If you click a few times, you'll see your "calls remaining" value decrease.

Again point out that this is all baked in.

### ToDo:
Leaving a note for myself on what I want to add next to this doc - I'll remove this later, but it gives you an idea of how I see this document moving on/wrapping up:

* Go to the Dev Portal (this will be installed already on the shared server Joe is setting up)
* Show signing up to get API access (don't forget you can use throw away gmail aliases like raymondcamden+testsomething@gmail.com)
* Show the docs and subscribing to a product to get APIs
* Show the *Admin* portion of the portal so you can show stats.

