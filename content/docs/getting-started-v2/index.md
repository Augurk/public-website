---
title: "Getting Started with Augurk 2"
date: 2019-02-01T08:20:19+01:00
icon: "directions"
featured: true
draft: false
weight: 1000
heroHeading: 'Getting Started with Augurk 2'
heroSubHeading: 'How to setup Augurk 2 in your organization'
heroBackground: ''
---
This document describes how you can setup *Augurk 2* within your own organization and start getting additional benefits out of your living documentation.

{{< callout-box >}}
This document describes how to get started with the 2.x versions of Augurk. A lot has changed with Augurk 3.0, so if you're only now getting started with Augurk we recommend you follow the guide for [Augurk 3.0]({{< relref "getting-started-v3" >}}).
{{< /callout-box >}}

## Prerequisites

In order to install *Augurk* you'll need to have the following:

* Windows Server 2016 or 2019
* Internet Information Services (IIS)
* .NET Framework 4.5 or higher

{{< callout-box >}}
The authors of Augurk are fully aware that these prerequisites don't come easy, especially for development teams that do not use .NET. Work is therefore currently underway with Augurk 3.0 to lower the barrier for entry.
{{< /callout-box >}}


Make sure you install the ASP.NET 4.6 extensibility feature for IIS when setting it up, since this is a requirement of *Augurk*.

## Installing Augurk

Once the prerequisites above have been setup we can begin installing Augurk. The [latest](https://github.com/augurk/augurk/releases/latest) version of Augurk can be downloaded from our [GitHub Releases Page](https://github.com/augurk/augurk/releases/). From there, download the *Augurk.WebDeploy.zip* to the machine you want to install Augurk on.

Next extract the zip file to a location on the machine and open the extracted contents. You'll find a folder called **Content** in here which contains a deeply nested folder structure. Navigate through this folder structure until you find a folder that contains the *Web.config* file:

{{< figure src="extracted-folder-contents.png" title="Extracted folder contents" >}}

Copy the contents of this folder to another location on the machine, for example **C:\inetpub\wwwroot\Augurk**. Then add an Application to IIS pointing to this folder:

{{< figure src="adding-iis-application.png" title="Adding IIS application" >}}

Then open the *Web.config* file in which you'll find the following section:

{{< highlight xml >}}
<connectionStrings>
    <add name="RavenDB" connectionString="$(ReplacableToken_RavenDB-Web.config Connection String_0)" />
</connectionStrings>
{{< /highlight >}}

Modify this section to read as follows, but feel free to choose a different name for the data dir.

{{< highlight xml >}}
<connectionStrings>
    <add name="RavenDB" connectionString="DataDir = ~\FeatureStore" />
</connectionStrings>
{{< /highlight >}}

Finally edit the security permissions of the folder you've extracted Augurk to so that the user account that the application pool associated with the Augurk application runs under has full access to it. If all settings so far have been kept at their default values the account should be **IIS AppPool\DefaultAppPool**.

{{< figure src="setting-security-permissions.png" title="Setting security permissions" >}}

After that, navigate to the URL Augurk is now installed at (ie. http://localhost/Augurk) and the homepage of Augurk should now be visible.

{{< figure src="augurk-homepage.png" title="Augurk Homepage" >}}

## Authentication

By default Augurk does not come with an authentication mechanism. This is by design since we want to make your living documentation to be as easily accessible as possible. That being said, you might still want to secure access to Augurk especially if the server it is running on is accessible from the public internet. To do this, you can setup Windows Authentication through IIS to require authentication.

To do so, first make sure you've installed the Windows authentication feature of IIS. This can be done by using the Server Manager. From there choose the Add roles and features option and follow the wizard until you get to the Server roles step.

{{< figure src="installing-windows-authentication.png" title="Installing Windows authentication" >}}

After installing that feature, enable Windows authentication within IIS for the application you've created in the previous section. Be sure to also disable Anonymous authentication, otherwise users will still be able to access Augurk without logging in.

{{< figure src="enabling-windows-authentication.png" title="Enabling Windows authentication" >}}

If necessary authentication can also be applied at a more fine grained level. For example, perhaps it's fine if anyone can read the contents of your instance of Augurk, but you want to restrict the upload of new content to specific users or groups. This can be done by editing the Web.config and adding something like the following:

{{< highlight xml >}}
<system.web>
    <authorization>
        <deny users="?" verbs="POST, PUT" />
    </authorization>
</system.web>
{{< /highlight >}}

{{< callout-box >}}
When mixing both anonymous and authenticated users, make sure to (re-)enable Anonymous authentication within IIS.
{{< /callout-box >}}

## Configuration
Once the installation is complete and authentication has been optionally setup, it's time to configure Augurk. From the homepage click on the gear icon in the upper right corner which will take you to the configuration page.

{{< figure src="configuration-page.png" title="Augurk's configuration page" >}}

In here you can set a couple of options:

* **Customization - Instance name**: Customize the instance name of your installation. This name is displayed in the upper left corner everywhere.
* **Configuration - Expire features**: Once you start using Augurk within your day to day work you might find that you accumulate a lot of data. To aid with cleaning up some older data, this feature allows you to automatically get rid of older feature files. You can control the amount of days to keep features, and control to which versions the retention policy applies.
* **Configuration - Show feature dependencies (Alpha)**: Allows you to enable an experimental feature within Augurk.
* **Import / Export**: These features allow you to import or export data to and from other instances of Augurk. For example, when moving Augurk to another server, you might want to export the data from the old instance and import it into a new installation.

## Uploading your first feature(s)

With configuration out of the way it's now time to upload your first feature file(s). There are various ways to do this, which are explained in more detail in the [Integration]() document, but for now we'll use **Augurk**'s command line interface (CLI).

The easiest way to acquire the Augurk CLI is to install it as a .NET Core Global Tool. This requires you to have [.NET Core](https://www.dot.net) installed. Alternatively you can download the [latest](https://github.com/augurk/Augurk.CommandLine/releases/latest) release from the [GitHub Releases page](https://github.com/augurk/Augurk.CommandLine/releases/). For this guide we'll assume you're going to use the .NET Core Global Tool.

Open up your favorite command line and let's get started with installing the Augurk CLI:

{{< highlight shell >}}
$ dotnet tool install -g Augurk.CLI
You can invoke the tool using the following command: augurk
Tool 'augurk.cli' (version '4.0.0') was successfully installed.
{{< /highlight >}}

Once installed you can use the **augurk** command to do various actions against an Augurk installation. One of them is the **publish** command that allows you to upload a feature file to Augurk. For example, run the following command:

{{< highlight shell >}}
$ augurk publish --featureFiles MyFeature.feature --productName MyProduct --groupName MyGroup --url http://localhost/Augurk
Starting publishing of feature files...
Successfully published feature 'MyFeature' version '0.0.0' for product 'MyProduct' to group 'MyGroup'.
Done publishing feature files.
{{< /highlight >}}

After that you should be able to see the feature file within Augurk:

{{< figure src="feature-in-augurk.png" title="Feature inside Augurk" >}}

## Next steps
Now that you've setup Augurk and published a feature file, feel free to have a look around Augurk and discover it's features. When you're ready to take the next step, read the [Integration]({{< relref "integrate" >}}) document to discover how you can automate the publishing of features files.