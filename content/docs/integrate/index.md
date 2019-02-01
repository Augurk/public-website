---
title: "Integrate"
date: 2019-02-01T12:56:12+01:00
icon: "plug"
featured: true
draft: false
weight: 200
heroHeading: 'Integrate'
heroSubHeading: 'Find out how to integrate Augurk into your development workflow.'
heroBackground: ''
---
Augurk works best when you integrate it with your existing development workflow. Find out what the options are for integrating and which option is the best for your project on this page.

## Introduction
There are many ways of integrating with Augurk. As shown in the [Getting Started]({{< relref "getting-started" >}}) guide the simplest way is using our [command line interface]({{< relref "cli" >}}) (CLI). While that works well for occasional use, it isn't very practical to have to upload your feature files manually each time a change is being made. We therefore recommend integrating Augurk into a continuous integration and/or continuous deployment pipeline.

This can of course be done using the CLI directly, but we also provide an out-of-the-box integration with [Azure DevOps (Server)](https://azure.microsoft.com/en-us/services/devops/) to make it really easy to get started for teams that are using that service.

{{% callout-box %}}Integrations with other services might be provided in the future.{{% /callout-box %}}

## Azure DevOps (Server)
The extension for Azure DevOps can be installed from the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=augurk.augurk). It works with both the cloud hosted version as well as the on-premises installation.

TODO: Insert screenshot from Marketplace.

After installing the extension a total of 4 new build and release tasks are available for use within your pipelines. We'll focus on the first two for now:

* Install Augurk CLI
* Run Augurk CLI

Azure DevOps provides two ways of running your pipelines, either using hosted agents (managed by Microsoft) or using your own agents. When using the hosted agents you will need to use both of the tasks above. If you have your own agents use of the Install task is optional.

### Install Augurk CLI task
This task is responsible for downloading a (configurable) version of the Augurk CLI and making it available for use during the build or release. It requires a single parameter to be configured namely the version of the CLI you want to use. This allows you to pin the version you're using.

TODO: Insert screenshot of configuration for Install CLI task

{{% callout-box %}}We would like our users to be able to select a version from the available releases in a dropdown, but unfortunately this isn't currently possible. For now, please refer to the [GitHub Release Page](https://github.com/augurk/Augurk.CommandLine/releases) for the CLI to find the version you want to use. It's recommended to use a stable version.{{% /callout-box %}}

### Run Augurk CLI task
By using this task you can kick off the Augurk CLI during a build or release. All of the commands that the CLI supports can be run using this task. In essence this task provides a more convenient way of running the CLI.

TODO: Insert screenshot of configuration for Run Augurk CLI task

Before using the task you need to configure a Service Endpoint that points to your installation of Augurk. If you haven't setup Augurk yet, refer to the [Getting Started]({{< relref "getting-started" >}}) guide to do so. Then add it by using the New button and entering the URL as well as a memorable name.

Next chose the *command* you want to run. For most pipelines this will be the **Publish** command, which is the default. To be able to publish you'll need to enter a *product name*. This will be the name you'll see in Augurk itself once features have been published for it. If you already have existing products in Augurk you'll also have the possibility to choose one of them from the dropdown.

Below the product name is a checkbox allowing the use of a folder structure to determine the names of the groups you'll see in Augurk. For example, consider the following directory structure:

{{< highlight shell >}}
Specifications
+-- Features
|   +-- GroupA
|   |   +-- Feature1.feature
|   |   +-- Feature2.feature
|   +-- GroupB
|   |   +-- Feature3.feature
{{< /highlight >}}

When the *Use folder structure* option is checked, the task will run the Augurk CLI twice: once for the features in GroupA and once for the features in GroupB. They will show up in Augurk under those groups.

{{% callout-box %}}You cannot use this option to publish feature files for two different products at the same time. If you want to do this, add two instances of the Run Augurk CLI task to your pipeline.{{% /callout-box %}}

Alternatively you can also publish all feature files to a single group by leaving this option unchecked and entering a group name manually or selecting one of the existing groups from the dropdown.

Next choose the set of feature files that will be uploaded. By default every feature file in every folder within your source repository is selected. Depending on your needs, you might want to change this to be more specific. If necessary you can also select a specific file or folder using the browse button. If your feature files aren't written in English, you might want to change the *Language* setting, otherwise the CLI might not be able to parse the feature files.

{{% callout-box %}}It's also possible to specify the language of a feature file inline in the file itself. To do this, add the #language:&lt;language-code&gt; instruction to the top of the feature file. This is especially useful if you have a mix of different languages in your feature files.{{% /callout-box %}}

When referencing image files from your feature files using Markdown, you might also want to check the *Embed images* option. This will embed the images during the upload to Augurk so that they can be viewed directly from there.

Finally specify a version that will be attached to the uploaded feature files. This allows you to see multiple different versions of the feature file within Augurk. It is recommended to use a variable here, such as $(Build.BuildNumber). Augurk supports semantic versioning and understands the different between stable and pre-release versions.

Additionally you might want to provide users of Augurk with a landing page for a product so that they can get a quick overview of what the product is for. To do this, add a Markdown file to your repository and enable the *Include product description* on the build task. Then choose the location of the previously created markdown file in the *Product description* option, or enter its path manually. When the pipeline is run, the markdown will be uploaded to Augurk and will be visible there when you click on the product in the top menu.