---
title: "Getting Started with Augurk"
date: 2019-02-01T08:20:19+01:00
icon: "directions"
featured: true
draft: false
weight: 100
heroHeading: 'Getting Started with Augurk'
heroSubHeading: 'How to setup Augurk in your organization'
heroBackground: ''
---
This document describes how you can setup *Augurk* within your own organization and start getting additional benefits out of your living documentation.

{{< callout-box >}}
This document describes how to get started with the 3.x versions of Augurk. If you're looking for documentation on getting started with older versions of Augurk, please look [here]({{< relref "getting-started-v2" >}}).
{{< /callout-box >}}

{{< callout-box class="warning" >}}
Augurk 3.0 uses a newer version of its embedded RavenDB, which isn't compatible with the older versions. Therefore, if you're upgrading from a version of Augurk prior to 3.0, be sure to read our [migration guide](TODO) on how to get your data imported from your existing installation after using this guide to install 3.0. 
{{< /callout-box >}}

# Choosing an installation type
Starting with Augurk 3.0 there are several different ways to install and host Augurk. Depending on your organization, some ways might make more sense than others. The options are:

* [Docker containers](#docker-containers)
* [Self-hosted](#self-hosted)
* [Hosting in IIS](#hosting-in-iis)

Most of these options are available both when hosting Augurk on-premises as well as in the cloud. Regardless of the method you choose, you'll want to continue reading about [configuring](#configuration) your Augurk installation.

{{< callout-box class="warning" >}}
Note that Augurk doesn't come with an authentication option out-of-the-box. Therefore, when hosting Augurk in the cloud, make sure you put some kind of authentication mechanism in front. For example, when hosting using Azure App Service, use its authentication and authorization features
to control access to Augurk.
{{< /callout-box >}}

## Docker containers
Running Augurk as a Docker container is a very easy way to get started. You'll only need to install Docker on your machine (like [Docker for Windows](https://docs.docker.com/docker-for-windows/) or [Docker for Mac](https://docs.docker.com/docker-for-mac/)) and then run the following command from the command line:

{{< highlight shell >}}
$ docker run -d -p 8080:80 augurk/augurk:3.0.0
{{< /highlight >}}

This will automatically pull the Augurk Docker image (the stable 3.0.0 release in this case) and run it in the background. You should be able to access Augurk by browsing to http://localhost:8080.

By default, Augurk uses an embedded RavenDB database for storing its data. When running Augurk in a container, the stored data will be lost if the container is deleted. To store the data more permanently we can use a [Docker Volume](https://docs.docker.com/storage/volumes/) to store the data outside of the container. To do this, use the following command:

{{< highlight shell >}}
$ docker run -d -p 8080:80 -v /path/on/host:/app/data augurk/augurk:3.0.0
{{< /highlight >}}

{{< callout-box >}}
When using Docker for Mac this doesn't work due to a limitation of RavenDB. It does work with Docker for Windows however. For production purposes we recommend running Augurk on a Linux Docker host.
{{< /callout-box>}}

To get the latest and greatest version of Augurk, use the *latest* tag instead of specific version. Do note that the latest might not be stable, since the latest tag always points at the latest development build of Augurk. For production scenario's we recommend using a specific version.

{{< callout-box class="warning" >}}
Augurk hasn't yet been tested to run on container orchestrators, such as [Kubernetes](https://kubernetes.io/), nor has it been tested to run in the cloud with things like [Azure Container Instances](https://azure.microsoft.com/en-us/services/container-instances/) or [Azure Web App for Containers](https://azure.microsoft.com/en-in/services/app-service/containers/). If you encounter issues with running Augurk in these environments, please let us know by filling an [issue](https://github.com/Augurk/Augurk).
{{< /callout-box>}}

## Self hosted
Another easy way to get started with running Augurk is by running it directly on your machine. To do so, you'll need to install [.NET Core](https://www.dot.net) on your machine. As of this writing, Augurk 3.0 targets [.NET Core 2.2](https://dotnet.microsoft.com/download). After installing the .NET Core Runtime on your machine, download Augurk from our [GitHub Releases Page](https://github.com/Augurk/Augurk/releases) and unzip it to a location on your machine. Then run the following command from the commandline in the folder where you've unzipped Augurk:

{{< highlight shell >}}
$ dotnet Augurk.dll
Hosting environment: Production
Content root path: /path/to/Augurk
Now listening on: http://localhost:5000
Now listening on: https://localhost:5001
Application started. Press Ctrl+C to shut down.
{{< /highlight >}}

You should then be able to navigate to http://localhost:5000/ or https://localhost:5001/ to access Augurk. If you want to run Augurk on a different port, you can use set the *ASPNETCORE_URLS* environment variable, so for example:

{{< highlight shell >}}
$ export ASPNETCORE_URLS=http://localhost:8000/
$ dotnet Augurk.dll
Hosting environment: Production
Content root path: /path/to/Augurk
Now listening on: http://localhost:8000
Application started. Press Ctrl+C to shut down.
{{< /highlight >}}

After pressing Ctrl+C Augurk will shutdown, but all data will be saved (unlike running in a Dockter container).

## Hosting in IIS
Augurk always had the option of hosting it inside IIS and of course this is no different with Augurk 3.0. However, since Augurk 3.0 now runs on .NET Core, you will need to install the [ASP.NET Core Runtime & Hosting Bundle](https://dotnet.microsoft.com/download/dotnet-core/2.2).

{{< callout-box >}}
Augurk runs on .NET Core 2.2, which includes In-Process hosting for IIS which Augurk uses by default.
{{< /callout-box >}}

After installing the runtime & hosting bundle, download the latest release of Augurk from our [GitHub Releases Page](https://github.com/Augurk/Augurk/releases) and unzip it somewhere on a machine that has IIS installed. Then add an application to IIS and point it at the folder where you've extracted Augurk.

{{< figure src="adding-iis-application.png" title="Adding IIS application" >}}

You should then be able to browse to Augurk by navigating to the appropriate URL (which depends on where you've created the IIS application).

### Authentication

By default Augurk does not come with an authentication mechanism. This is by design since we want to make your living documentation to be as easily accessible as possible. That being said, you might still want to secure access to Augurk especially if the server it is running on is accessible from the public internet. To do this, you can setup Windows Authentication through IIS to require authentication.

To do so, first make sure you've installed the Windows authentication feature of IIS. This can be done by using the Server Manager. From there choose the Add roles and features option and follow the wizard until you get to the Server roles step.

{{< figure src="../getting-started-v2/installing-windows-authentication.png" title="Installing Windows authentication" >}}

After installing that feature, enable Windows authentication within IIS for the application you've created in the previous section. Be sure to also disable Anonymous authentication, otherwise users will still be able to access Augurk without logging in.

{{< figure src="../getting-started-v2/enabling-windows-authentication.png" title="Enabling Windows authentication" >}}

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
Regardless of the installation method used, you should be able to access Augurk and see the homepage:

{{< figure src="../getting-started-v2/augurk-homepage.png" title="Augurk Homepage" >}}

Once the installation is complete and authentication has been optionally setup, it's time to configure Augurk. From the homepage click on the gear icon in the upper right corner which will take you to the configuration page.

{{< figure src="../getting-started-v2/configuration-page.png" title="Augurk's configuration page" >}}

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

{{< figure src="../getting-started-v2/feature-in-augurk.png" title="Feature inside Augurk" >}}

## Next steps
Now that you've setup Augurk and published a feature file, feel free to have a look around Augurk and discover it's features. When you're ready to take the next step, read the [Integration]({{< relref "integrate" >}}) document to discover how you can automate the publishing of features files.

If you're upgrading from a previous version of Augurk, please read our [migration guide](TODO) for instructions to getting your existing data imported into the new version.