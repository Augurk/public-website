# Contribute to Augurk Public Webiste
Here is what you'll need to know to start contributing to this repository.

## Cloning the repository
The first thing to do is to clone the repository. Since this repository uses Git submodules, you'll want to make sure you include them when you clone the repository. For example, using the following command:

```bash
git clone --recurse-submodules  https://github.com/augurk/public-website
```

**Note:** Replace the clone URL with your fork as necessary.

## Running locally
First of all, make sure you have [Hugo](https://gohugio.io/) installed on your machine. Please refer to the official [Hugo Documentation](https://gohugo.io/getting-started/quick-start/) to get setup quickly.

Once you have Hugo installed locally, run the following command:

```bash
hugo serve
```

This should spin up a web server that you can browse to locally (usually at http://localhost:1313/). As you make changes to the **content** they should show up immediately.

## Content types
The website currently uses the following content types to differentiate between sections of the website:

- Docs: Used for documentation on how to setup and use Augurk
- Blog: Used for blogposts related to Augurk

You'll find these as subfolders inside the **content** folder, and the related layouts inside the **layouts** folder.