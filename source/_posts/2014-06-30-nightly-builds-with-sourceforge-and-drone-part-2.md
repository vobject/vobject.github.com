---
layout: post
title: Nightly Builds with Sourceforge and Drone (Part 2)
date: 2014-06-30 20:43:35
comments: true
tags: [Sourceforge, Drone, Nightly Builds, Continuous Integration]
categories:
---

In the [previous part](/blog/2014/05/29/nightly-builds-with-sourceforge-and-drone-part-1/) of this mini series I outlined the objective to get nightly builds utilizing only free services.
It showed a high level description of how it ought to be accomplished using Sourceforge, BitBucket, and Drone. As a short recap, this is the scenario we are dealing with:

1. An existing project **ci-test** is hosted on [Sourceforge](https://sourceforge.net/).
2. **ci-test** uses [Mercurial](https://sourceforge.net/p/forge/documentation/Mercurial/).
4. **ci-test** depends only on cross platform libraries.
5. **ci-test** is open source.

This part describes how to setup and configure the different services to provide the workflow we want.


# Setting up BitBucket

Create and account at [BitBucket](https://bitbucket.org/). That's all.

This account only serves as a helper to set up a project at [Drone](https://drone.io/). Drone does not support Sourceforge (or the other way around), so an account is needed to create a Drone project. An account at [GitHub](https://github.com/) would serve just as well. But for some unknown reason I decided to write this from the BitBucket perspective...

Having an account, create a proxy project for the real project hosted on Sourceforge. Let's call the project "ci-test-nightly" and create a README.md file containing links to the SF repo. The proxy project does not include any other data for now. (In the following part 3 I will recommend storing working versions of the Drone build script here; but that is not a concern right now.)

I cases where the original repository is already hosted on BitBucket or GitHub (and not on SF), this step can be omited becomes you already have an existing project that can be used to create a Drone project from.


# Setting up Drone

Create an account at [Drone](https://drone.io/). (You can also login by using your existing BitBucket or GitHub account but I did not try that.)
From the Drone account create a new build project from the previously created proxy project and configure it.

## Build & Test
1. Select the programming **Language** of the hosted project, e.g. C/C++.
2. Notive the **Commands** text control but leave it empty for now. This is where we will write the actual build script once everything is properly set up.
{% img /images/nightly-builds-with-sourceforge-and-drone/drone_build_test.jpeg 'Drone Build & Test configuration' %}

## Artifacts
Leave empty. The build artifacts will be uploaded and stored on SF servers after a build.

## Status Badge
Add the Markdown badge to the README.md file in the original and/or proxy project.

## Repository
1. Make sure the project is **Active**.
2. The build hook can be used to trigger a build from any web browser without the need to push commits or login into the Drone account. Keep this link in mind. We will need it when setting up SF.
3. Set the **Branch Filter** to _default_ (Mercurial) or _master_ (Git) depending on what the proxy project uses.
4. The **View Key** button shows the projects key that will allow the Drone build server to authenticate itself at Sourceforge. We will use it in a minute.
{% img /images/nightly-builds-with-sourceforge-and-drone/drone_repo.jpeg 'Drone Repository configuration' %}


# Setting up SourceForge

register Drone SSH key with SF account
create SSH key to login with putty and how to login with putty (with screenshots)
create artifacts folder and Mercurial hook via SF shell

{% codeblock %}
[hooks]
; = [the next line is required for site integration, do not remove/modify] =
changegroup.sourceforge = curl -s http://sourceforge.net/auth/refresh_repo/p/opentomb/code/
; hook to send out trigger for nightly builds
changegroup.nightly = ./.hg/nightly-mail
{% endcodeblock %}



{% codeblock %}
#!/bin/sh
DEST=$(hg log --template '{author|email}' -r $HG_NODE)
SUBJECT='OpenTomb: Request Nightly Build'

echo 'https://drone.io/hook?id=bitbucket.org/vobject/opentomb-nightly&token=01UBbXqUQNX4uzxznRY8' | mail -s "$SUBJECT" $DEST
{% endcodeblock %}



## Testing the hook

commit something to the SF repo to get the email.


# Notes

The guy pushing the hook gets the request-build email.

There are people that have not configured their email inside the Mercurial.ini/.hgrc/.gitconfig. In this case, they will not get the email.

I had no problems with user access / ownership rights on the SF shell

I could not call the Build-Link directly from inside the hook using wget or curl. If this were possible, the system could be fully automated (without email).

Haven't tried a different branch but default.

Havent't tried the whole thing with travic-ci (but might soon -- or switch Maru to Drone).

Check out opentomb on BB, Drone, SF.

when only a single developer works/commits on the project, the email hook can be omited, because the dev also owns te drone accound and can always trigger a build when he wants to.

my RSA SSH key did not work with putty/SF. had to create a new one.

had problems with SF sometimes not sending the emails. in this case: just trigger the link manually from the Drone settings.


# Future

The final part of this mini-series will go into the compilation that takes place on the Drone applience. We will explore how to cross compile a SDL2 application and upload its artifacts to SourceForge.


# Reference

SF Shell access
Putty, SSH, public/private keys
Sending Email through pipes
Mercurial Links (.hgrc, hooks, template)
