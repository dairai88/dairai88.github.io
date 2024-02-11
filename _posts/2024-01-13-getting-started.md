---
title: Getting Started
date: 2024-01-12 14:00:00
categories: [Blogging, Tutorial]
pin: true
tags: [getting started]
---

## Prerequisites
Follow the instructions in the [Jekyll Docs](https://jekyllrb.com/docs/installation/) to complete the installation of the basic environment. [Git](https://git-scm.com/) also needs to be installed.

## Installation

### Creating a New Site

There are two ways to create a new repository for this theme:

- [**Using the Chirpy Starter**](#option-1-using-the-chirpy-starter) - Easy to upgrade, isolates irrelevant project files so you can focus on writing.
- [**GitHub Fork**](#option-2-github-fork) - Convenient for custom development, but difficult to upgrade. Unless you are familiar with Jekyll and are determined to tweak or contribute to this project, this approach is not recommended.

#### Option 1. Using the Chirpy Starter

Sign in to GitHub and browse to [**Chirpy Starter**][starter], click the button <kbd>Use this template</kbd> > <kbd>Create a new repository</kbd>, and name the new repository `USERNAME.github.io`, where `USERNAME` represents your GitHub username.

#### Option 2. GitHub Fork

Sign in to GitHub to [fork **Chirpy**](https://github.com/cotes2020/jekyll-theme-chirpy/fork), and then rename it to `USERNAME.github.io` (`USERNAME` means your username).

Next, clone your site to local machine. In order to build JavaScript files later, we need to install [Node.js][nodejs], and then run the tool:

```console
$ bash tools/init
```

> If you don't want to deploy your site on GitHub Pages, append option `--no-gh` at the end of the above command.
{: .prompt-info }

The above command will:

1. Check out the code to the [latest tag][latest-tag] (to ensure the stability of your site: as the code for the default branch is under development).
2. Remove non-essential sample files and take care of GitHub-related files.
3. Build JavaScript files and export to `assets/js/dist/`{: .filepath }, then make them tracked by Git.
4. Automatically create a new commit to save the changes above.