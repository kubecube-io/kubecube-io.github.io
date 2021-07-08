# KubeCube docs

Welcome! This repository houses all the assets required to build the KubeCube website and documentation. We're pleased that you want to contribute! The website is hosted at [https://kubecube.io](https://kubecube.io).

We use [Hugo](https://gohugo.io/) to format and generate our website, the [Docsy](https://github.com/google/docsy) theme for styling and site structure. Thanks!.

## Contribution

1. Fork this repository

2. Clone the code and submodules

   ```
   $ git clone --recurse-submodules https://github.com/kubecube-io/kubecube-io.github.io.git
   ```

3. Make your changes and send a pull request (PR).

4. Merge the pull request

   Once your pull request is created, a KubeCube reviewer will take responsibility for providing clear, actionable feedback, re-improve and merge.

## Previewing your changes on a local website server

If you'd like to preview your doc updates as you work, you can install Hugo and run a local server to host your website. This section shows you how.

### Install Hugo and other dependencies

You need Hugo version 0.81or later, and it must be the **extended** version of Hugo. 

To get the latest extended version of Hugo:

1. Go to the [Hugo releases](https://github.com/gohugoio/hugo/releases) page.
2. In the most recent release, scroll down until you find a list of **extended** versions.
3. Download the relevant file for your operating system.
4. Unzip the downloaded file into a location of your choice.

For more details about installing Hugo, See the [Hugo installation guide](https://gohugo.io/getting-started/installing/).

**Not Necessary:**If you plan to make changes to the site styling, you need to install some **CSS libraries** as well. Follow the instructions in the [Docsy theme's setup guide](https://www.docsy.dev/docs/getting-started/#install-postcss).

### Run a local website server

1. Fork this repository and clone it

	```
	$ git clone --recurse-submodules https://github.com/kubecube-io/kubecube-io.github.io.git
	$ cd kubecube-io.github.io
	```
	
2. Start your website server. Make sure you run this command from the `kubecube-io.github.io` directory, so that Hugo can find the config files it needs:

   ```
   hugo server
   ```

3. You can access your website at http://localhost:1313/ (bind address 127.0.0.1)

After clone this repository ,Make sure you run this command from the `/website/` directory, so that Hugo can find the config files it needs:`kubecube-io.github.io`

## Useful docs

- [User guide for the Docsy theme](https://www.docsy.dev/docs/getting-started/)
- [Hugo installation guide](https://gohugo.io/getting-started/installing/)
- [Hugo basic usage](https://gohugo.io/getting-started/usage/)
- [Hugo site directory structure](https://gohugo.io/getting-started/directory-structure/)
- [hugo server reference](https://gohugo.io/commands/hugo_server/)

## Thanks

KubeCube thrives on community participation, and we really appreciate your contributions to our site and our documentation!

