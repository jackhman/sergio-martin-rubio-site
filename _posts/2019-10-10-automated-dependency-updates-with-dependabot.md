---
title: Automated Dependency Updates With Dependabot
tags: [Git]
style: border
color: dark
description: Through the integration of Dependabot you can automate dependecy updates on your projects. Do you want to know more?
---

{%- capture list_items -%}
Introduction
How to use
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

[Dependabot](https://dependabot.com) is a free and open source tool that has been bought by _GitHub_ and allows you to check for updates on you dependency files.

It can be used to update dependencies in _Ruby_, _JavaScript_, _Python_, _PHP_, _Elixir_, _Elm_, _Go_, _Rust_, _Java_ and _.NET_, as well as, git submodules, _Docker_ files and _Terraform_ files.

## Package Manger Options

- bundler
- pip (includes pipenv)
- npm_and_yarn
- maven
- gradle
- cargo
- hex
- composer
- nuget
- dep
- go_modules
- elm
- submodules
- docker
- terraform

## GitHub Integration Guide

Dependabot is fully integrated with GitHub and only in a few steps you will be able to add it to your current GitHub projects.

1.Go to [Dependabot site](https://dependabot.com) or [GitHub marketplace and search for Dependabot](https://github.com/marketplace?utf8=%E2%9C%93&query=dependabot).
2.Install Dependabot to your GitHub account.

{% include elements/figure.html image="https://lh3.googleusercontent.com/2LF49w-QRKtX7eEMkjZM83Dn6eJVvdKblx7dRomTj9QKBZ7zZHIf2BKDohN-1eXZmUU4DJUeibuE2FhSeg=w600-no-tmp.jpg" caption="Install Dependabot GitHub" %}

3.Give permission to all your repositories or selected ones.

{% include elements/figure.html image="https://lh3.googleusercontent.com/w-nGjPm9AMsVfvkcbSYhu9j482Z55OUdrhOSA6tFupSXRFv9tTD2f2ZAFn6AHRGzLC_MMDGHGK4t-aj8Fg=w600-no-tmp.jpg" caption="Install Dependabot Preview" %}

4.Start adding repositories to the Dependabot dashboard.

{% include elements/figure.html image="https://lh3.googleusercontent.com/j4edb8TzKBqykrV_j62qvPQ0ME5NCH3YCD--UynTcIwPdHzUZdxJEshexS-4IdIkX5Evil88lN_tGXNP-Q=w600-no-tmp.jpg" caption="Add Repos Dependabot" %}

5.Tweat Dependabot settings for your projects: how frequent it runs (daily, weekly...), directory, branch, GitHub Pull Requests info...

{% include elements/figure.html image="https://lh3.googleusercontent.com/WTi5NcbLw96prl8jRf9HXQ9FJJRmWQVJsddeGyLXG_5Asqcdw9Vqdv5-esoIN_L1tb7Ps92MLW27zOUkyw=w800-no-tmp.jpg" caption="Depedabot Project Settings" %}

6.Run Dependabot and review PR. A relevant branch name, PR title, comment and labels are created.

{% include elements/figure.html image="https://lh3.googleusercontent.com/cnB2V_3V22hqUt4vH57167KfX-bKA7Z75523xiZfIdwbvGnx5ykJNBaHiqGLDHRujoVP_nUSZ7-9s5C6kw=w800-no-tmp.jpg" caption="Depedabot Project Settings" %}

## Advance Integrations

Dependabot can be also used by itself. The oficial repository provides a script to run it with Docker or have a repository dedicated to the script and configure a GitLab CI pipeline to run it periodically.
