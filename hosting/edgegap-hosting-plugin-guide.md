---
description: Mirror's inofficial Edgegap Hosting Plugin documentation.
---

# Edgegap Hosting Plugin Guide

Thanks to **Edgegap**'s hosting plugin for Unity, Mirror users get 0.5 vCPU cloud hosting for free!

You can build & launch a game server into the cloud directly from your Unity Editor, without any Linux or Cloud usage whatsoever.

<figure><img src="../.gitbook/assets/2023-11-03 - 17-31-21@2x.png" alt=""><figcaption><p>Edgegap Hosting Plugin</p></figcaption></figure>

{% hint style="warning" %}
This is **cutting edge**, **next gen**, **black magic** technology with a few rough edges!

If you encounter issues, please report them in our Discord's **#edgegap** channel.

\=> Today, the plugin requires \~30 clicks and has a few issues.

\=> Long term, more and more steps will be automated until it's only 1 click!

\
Enjoy a preview of the future, and please be patient with our 🇨🇦 Canadian friends!
{% endhint %}

## Overview

Setup will take only a few minutes, with 4 basic steps:

1. **Edgegap.com** Account + Application setup
2. Installing Unity **Linux** Build Support & **Docker** Desktop
3. Configuring the Unity **Plugin**
4. **Building & Pushing** our server to Edgegap

## Open the Plugin in Unity / Mirror

As of November 2023, Mirror now has Edgegap's hosting plugin included by default.\
Originally it comes from the Unity Asset Store, but we forked it to apply a few fixes on top!

{% embed url="https://assetstore.unity.com/packages/tools/network/edgegap-cloud-server-212563" %}

If you are on an older Mirror version, you can also download the Assets/Mirror/Hosting folder from our Github repository manually. If you see errors about missing 'Newtonsoft Json', add this package to your Package Manager:

<figure><img src="../.gitbook/assets/2023-11-05 - 13-14-02@2x.png" alt=""><figcaption></figcaption></figure>

You can also open Packages/manifest.json and add this line manually:

```json
"com.unity.nuget.newtonsoft-json": "3.2.1"
```

By default, this should work out of the box though.

{% hint style="info" %}
We do not recommend to use the plugin's Asset Store version. Instead, download latest Mirror from Github, or simply download the Assets/Mirror/Hosting folder from Github and drop it into your Mirror project.
{% endhint %}

Once you have, check for "**Edgegap** -> **Edgegap** Hosting" in Unity's top menu:

<figure><img src="../.gitbook/assets/2023-11-03 - 17-49-38@2x.png" alt=""><figcaption></figcaption></figure>

## Create an Edgegap Account

As mentioned, Edgegap gives you 0.5 vCPU for free. In other words, 50% of a virtual CPU core in the cloud. That's enough to test simple games. We hope that this increases to 1 vCPU long term.

Of course, you can always have more vCPUs if you send them money.

For now, let's start by creating an account on the Edgegap website. It's free.

{% embed url="https://app.edgegap.com/auth/register" %}

## Get Registry Access

First, we need access to Edgegap's Docker registry in order to push our server builds there.

If you aren't familiar with Docker, don't worry about it.

For now, let's just get access to the registry.

Click Container Registry in the left menu, or follow this link:

{% embed url="https://app.edgegap.com/registry-management/repositories/list" %}

You'll probably see a screen where you can request access.&#x20;

Do request, and then it'll look like this:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-23-42@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
It may help to join our Discord's #edgegap channel and ask an Edgegap employee directly to speed up the manual approval.\
This step is supposedly **going to be automated** on around November 7th.
{% endhint %}

Once you have registry access, grab your **Project**, **Username** and **Token** from the **Container Registry** page. We'll need those in a minute:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-29-23@2x.png" alt=""><figcaption></figcaption></figure>

## Create an Application

Next, go to **Applications**:

<figure><img src="../.gitbook/assets/2023-11-03 - 17-54-09@2x.png" alt=""><figcaption></figcaption></figure>

Then click **Create application** in the top right and enter a **lowercase(!)** name like:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-00-37@2x.png" alt=""><figcaption></figcaption></figure>

The image doesn't matter. Just click **Create Application**.

Make sure to replace 'your-game' with your game name, and do that in all the following steps where it says 'your-game' as well. Continuing, fill out the application details exactly like this:

<figure><img src="../.gitbook/assets/2023-11-05 - 12-34-33@2x.png" alt=""><figcaption></figcaption></figure>

* **Version name**: just use exactly **v1**, don't worry about this. (**lowercase!)**
* **Registry**: use exactly **registry.edgegap.com** (**lowercase!)**, this is from the **Container Registry** page. You can enter a custom docker registry here, but you probably don't have one or don't even know what this even means. That's fine :)
* **Image repository**: first enter the **Project** part from the Container Registry page, and then add "/game". For example, it may look something like: "some\_username-v23r8h5472/your-game".
* **Tag**: 0.0.1 - use exactly this. Every new server build will automatically increase this to 0.0.2 etc.
* **vCPU**: 0.5 vCPU is free.
* **Memory**: 0.5 GB is free.
* **Private Registry User**: enter the Username and Token from the previous **Container Registry** page here. Otherwise we won't have access to push our server builds there later.

{% hint style="warning" %}
Note that as of November 3, 2023, modifying an Application's **Private Registry User** after creating an Application is broken. If you accidentally entered the wrong data, please create a new Application. Edgegap is aware of this bug and working on a fix.
{% endhint %}

Next, click **Create Version**. Note it says 'Version'. You have different Versions of your game, kind of like 'Counter-Strike 1.6, Counter-Strike GO, Counter-Strike 2' etc. For now, don't worry about versions. One is enough.

After clicking **Create Version**, you will see a validation error.

<figure><img src="../.gitbook/assets/2023-11-03 - 18-10-18@2x.png" alt=""><figcaption></figcaption></figure>

This is shown because we didn't upload a docker image to the registry yet. Don't worry about it. Just click **Continue Anyway.**

Next, it asks us to create a port. Use the one from your Unity Project's NetworkManager -> Transport. For example, let's do this with Mirror's **Tanks** demo:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-13-05@2x.png" alt=""><figcaption></figcaption></figure>

Kcp is **UDP** with port **7777** by default.\
If you use Telepathy, then that would be TCP with port 7777 by default.

Let's enter **7777** and **UDP** in our application.

We also disabled **Verifications** in order to keep things simple.

<figure><img src="../.gitbook/assets/2023-11-03 - 18-15-07@2x.png" alt=""><figcaption></figcaption></figure>

Finally, press **Submit**. There you go, your first application.&#x20;

It doesn't do anything yet, but we'll get there.

To compare, this is what it looks like now. The red "!" icon is still there because validation still fails, this is fine for now.

<figure><img src="../.gitbook/assets/2023-11-05 - 12-05-09@2x.png" alt=""><figcaption></figcaption></figure>

## Create a Token

Last step on the website, we need a **Token** for the Unity plugin later. Since we are here, let's just create it now.

On the **Edgegap** website, click your Organization on the bottom left (the head icon), click Tokens or follow this link [https://app.edgegap.com/user-settings?tab=tokens](https://app.edgegap.com/user-settings?tab=tokens) and then click **Create API Token**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-38-00@2x.png" alt=""><figcaption></figcaption></figure>

Name it **your-game-token**, enable **is Quick Start** and press **Submit**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-41-19@2x.png" alt=""><figcaption></figcaption></figure>

Afterwards click the Clipboard icon to copy it to your clipboard:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-41-52@2x.png" alt=""><figcaption></figcaption></figure>

## Configuring the Edgegap Unity Plugin

Alright, back to Unity. Let's punch in all the data in our Plugin. As mentioned, you can find it under **Edgegap** -> **Edgegap Hosting** in Unity's top menu. Let's configure it:

<figure><img src="../.gitbook/assets/2023-11-05 - 12-10-55@2x.png" alt=""><figcaption></figcaption></figure>

* **Token**: that's the one we just copied. Put it in there. It looks like "token abcd-efgh-ijkl....". Make sure to include the "token " part. If you just paste it, then it's fine as is.
* **API Environment**: use **Console**. We don't know why 'Staging' didn't work.
* **App Name:** your-game, or whatever you entered in your Application before.
* **App version**: v1 as we entered in our Application.

Don't worry about the grayed out data yet. Press **Connect** - you should see 'Connected' now.

Next, enter the other details from our Application:

<figure><img src="../.gitbook/assets/2023-11-05 - 12-13-08@2x.png" alt=""><figcaption></figcaption></figure>

* **Container Registry**: registry.edgegap.com from the **Container Registry** page.
* **Image Repository**: your-project/your-game or whatever you entered in your Application before. Remember that "your-project" is from the **Container Registry** page as well.
* **Tag**: 0.0.1 for now.&#x20;
* **Increment tag on build**: enable this. You always want to increase the tag for each new build to avoid caching issues. Seriously, don't push a new server build with an old tag, it's probably gonna launch the old build again due to caching.

{% hint style="info" %}
Note that Edgegap is working on a new plugin version which will automatically grab all the settings from your Application. This will be easier soon!
{% endhint %}

## Build and Push

Next, we are going to build our project as Linux game server, and create a Docker build, then push it to Edgegap. This is actually pretty easy, just need to install a few things really quick.

### Install Unity Linux Build Support

In your **Unity Hub**, select **Installs**, press the Configuration icon next to your Unity version and click **Add Modules**:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-52-53@2x.png" alt=""><figcaption></figcaption></figure>

Find and install **Linux Dedicated Server Build**. You may add the others too just to be safe:

<figure><img src="../.gitbook/assets/2023-11-03 - 18-54-28@2x.png" alt=""><figcaption></figcaption></figure>

Press Continue, wait, restart Unity once, done.

### Install Docker Desktop

Edgegap works with containers, which means we need to install Docker. We don't need to worry about it ever, we just need to install it once.

The easiest way is to simply install **Docker Desktop**:

{% embed url="https://www.docker.com/products/docker-desktop/" %}

Download it, install it, open it and leave it running. You can pretty much auto start it with your operating system each time.

{% hint style="warning" %}
Some users reported that they had to restart their computer once before they could use Docker. To be safe, consider restarting once!
{% endhint %}

{% hint style="info" %}
Quick explanation about Docker if you care. You don't need to know this, so feel free to skip.

Basically Docker is a super easy way to configure a virtual machine for your game server build. Previously you would manually create a VM in say Google Cloud, configure a hard disk, open ports, install a Linux version, run apt-update, install dependencies, ...

With docker, we just have a text file. It says 'install ubuntu, copy our build into the VM, navigate to the folder, run unity. Again, you DO NOT need to worry about this. The plugin creates this automatically, something like this (again, don't worry about it):

```
FROM ubuntu:bionic
ARG DEBIAN_FRONTEND=noninteractive
COPY Builds/EdgegapServer /root/build/
WORKDIR /root/
RUN chmod +x /root/build/ServerBuild
ENTRYPOINT [ "/root/build/ServerBuild", "-batchmode", "-nographics"]

```
{% endhint %}

Next, we need to log into Edgegap's docker registry that we previously requested access to.

We are working with Edgegap to automate this. For now you need to open a Terminal / Console:

* On Windows, hit CTRL+R, enter CMD, hit enter to open it.
* On Mac, open Finder, go to Applications -> Utilities -> Terminal.
* On Linux, you probably know how to do it.

{% hint style="info" %}
Don't be scared of the Terminal. It's just black background and white text where we'll enter exactly one command. This will be automated soon.
{% endhint %}

Grab your Username + Token from the **Container Registry** page once more.\
Now enter this simple terminal command. There won't be any others, promised!



```
docker login registry.edgegap.com
```

Then enter your Username and Token (as password) that you see on the Edgegap.com -> **Container Registry** page.

If you can't seem to login, you can also try this command instead:

```
docker login -u "YOUR_USERNAME" registry.edgegap.com
```

Docker Desktop remembers your login, so you won't have to do this again next time.

Alright, that's it for Docker.

## Back to the Unity Plugin

Next, go back to the Unity plugin and press **Build and Push**:&#x20;

<figure><img src="../.gitbook/assets/2023-11-05 - 12-20-39@2x.png" alt=""><figcaption></figcaption></figure>

You'll see a progress bar for a while. Unity will create a Linux build, then create a Docker build, then upload the whole thing to Edgegap. Note that uploading will take a while depending on your internet connection. The progress bar halts while uploading, but you can check your operating system's bandwidth usage to see if it's still uploading:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-17-10@2x.png" alt=""><figcaption></figcaption></figure>

If this all worked, then it'll simply finish without telling you. We are working on making this more obvious.

If this failed, it'll show you errors.

Here are a few common issues and workarounds:

* **Missing Linux Build Support**: install it in your Unity hub. Make sure you do it for the Unity version that you are using in your project. This generally works once you have the Linux Build Support installed.
* **Incremental Build Failed**: delete your previous Unity Linux build in the /Builds folder next to the /Assets folder, restart Unity try again. Delete your Library/ folder if you need to. This is a Unity bug that happens sometimes.
* **Docker authorization Failed**: make sure Docker Desktop is running and make sure that you are logged in with the above Terminal command. Also make sure that image registry consists of the **Container Registry**'s 'Project' + "/" + "your-game".

If you encounter other issues, talk in our **#edgegap** Discord channel. We want to find solutions for any possible issue and explain this here!

{% hint style="info" %}
If building failed, then the progress bar may get stuck forever. You'll have to force kill Unity with your task manager. This will be fixed very soon.
{% endhint %}

## Start Server

Once we finished building, simply press the **Start Server** button. It'll be **Deploying** for a while:

<figure><img src="../.gitbook/assets/2023-11-03 - 19-21-14@2x.png" alt=""><figcaption></figcaption></figure>

Eventually it'll be started:

<figure><img src="../.gitbook/assets/2023-11-05 - 13-01-05@2x.png" alt=""><figcaption></figcaption></figure>

This screenshot is from my other test application which uses TCP port 5000.

For you it should show you UDP port 7777, just as we configured in the Application.

Edgegap will assign you a random **External Port** that maps to your configured Port.

That is because they may have multiple deployments asking for the same port 7777.

But there's only one port 7777.

That's why they have External ports that are random.

Long story short, you may now connect your game!

## Connect your Game Client

Press **Play** in Unity, enter the **Server DNS** and **External Port** from the hosting plugin (there's a **Copy** button) and press **Client** to connect:&#x20;

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

**You should now be connected to your Deployment! 🚀**

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
It's important to understand the magic that is happening here.\
Not only can we easily launch our game server directly from within Unity.\
We can even launch thousands of them from the Edgegap website! 🤩
{% endhint %}

## Troubleshooting Connection Issues

If your Server Status says **Ready** but you can't seem to connect, try this:

* On the Edgegap website, go to Deployments -> select your Deployment -> select **Container Logs**, check the log files to see if your game server actually launched or if there are issues.
  * If it says "exec user process caused: no such file or directory": this can happen if you pushed an ARM build to Edgegap's x86 infrastructure. We already updated the plugin to properly cross compile from ARM so this generally should not happen anymore.
* If everything seems fine but you still can't connect, please talk to an Edgegap employee in the Mirror Discord's **#edgegap** channel.&#x20;

{% hint style="warning" %}
As mentioned, this is cutting edge technology, with lots of rough edges. You'll most certainly encounter errors and headaches for now. Please report any issues so Edgegap can fix them.
{% endhint %}

{% hint style="info" %}
We are aware that this guide was quite long.\
A lot of these steps will be automated over time.

Long term this will be a super easy way to host and scale your multiplayer game!
{% endhint %}

{% hint style="info" %}
Big thanks to our Canadian 🇨🇦 friends at **Edgegap** for giving Mirror users 0.5 vCPU for free!\
While we hope for a larger free tier in the future, please keep in mind that they are the ones who are paying for the infrastructure. If you have the means, please pay them for more vCPUs!
{% endhint %}