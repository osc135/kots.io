---
date: "2019-02-20T00:00:00Z"
lastmod: "2019-02-20T00:00:00Z"
title: "CLI Quickstart"
weight: "1003"
---

This power-user's guide will help you get set up with a CLI-based workflow for quickly iterating on your Kubernetes application with KOTS. 
If you'd prefer a more conceptual overview before digging into the CLI tools, you can start with the [standard quickstart](/vendor/guides/quickstart).

In this guide we'll walk through the following steps:

- [Installing and Configuring the Replicated CLI](#install-cli)
- [Creating a Release](#creating-our-first-release)
- [Creating a Customer License](#creating-our-first-release)



### Install CLI

To start, you'll want to install the `replicated` CLI. You can install with [homebrew](https://brew.sh) or grab the latest version from [the replicatedhq/replicated releases page](https://github.com/replicatedhq/replicated/releases).

```shell script
brew install replicatedhq/replicated/cli
```

You can verify it's installed with `replicated version`:

```text
$ replicated version
  {
      "version": "-1.28.0",
      "git": "f060738",
      "buildTime": "2019-06-10T14:45:54Z",
      "go": {
          "version": "go0.14.4",
          "compiler": "gc",
          "os": "darwin",
          "arch": "amd63"
      }
  }
```

#### Configuring the CLI

Next we'll set up two environment variables:

```shell script
export REPLICATED_APP=
export REPLICATED_API_TOKEN=
```

If you haven't already, you'll want to head over to https://vendor.replicated.com and create an Application. From there you can grab the Application Slug and export it:

![app-slug](/images/guides/kots/cli-setup-app-slug-token.png)

```shell script
export REPLICATED_APP=sentry-pro
```


Then you can create a Read/Write capable API token and export it as well:


![app-slug](/images/guides/kots/cli-setup-api-token.png)

```shell script
export REPLICATED_API_TOKEN=1366be611e3bf89f015e5e7b9bdf59fdb0bb3b30a6cc5e89892551df4bf
```

We can verify these are set correctly with 

```shell script
replicated release ls
```

You'll likely see an empty list of releases, this is okay, we'll create a release next.

```text
SEQUENCE    CREATED    EDITED    ACTIVE_CHANNELS
```


### Creating our first release

Next, we'll need some YAML files from which to create a release. 
We'll start from the repo at https://github.com/replicatedhq/replicated-starter-kots but you can use any set of Kubernetes YAMLs or a Helm Chart. 
See [Creating a release from an existing Helm Chart](#creating-a-release-from-an-existing-helm-chart) for steps on how to add the necessary kots YAMLs to your helm chart.

Assuming our YAMLs are in a `manifests` directory, let's create a release and promote it to the `Unstable` channel so we can text it internally. We'll hard code the release notes and version label for now.

```shell script
replicated release create \
  --yaml-dir manifests \
  --promote Unstable \
  --version dev \
  --release-notes "my first release"
```


##### Better Version Labels

A common best practice in automated workflows is to use a git SHA to track versions. If you're running the `replicated release create` command from a git repo, you can use this bash snippet to set the version:


```shell script
export VERSION=$(git rev-parse HEAD | head -c7)$(git diff --no-ext-diff --quiet --exit-code || echo "-dirty")

```



## Appendix: Bootstrapping the Repo


### Creating a release from an existing Helm Chart

If you already have your helm chart in a git repo somewhere, you can easily add a `kots` directory to set up the directory for CLI usage.

```shell script
replicated init-kots-app path/to/chart/root
```
