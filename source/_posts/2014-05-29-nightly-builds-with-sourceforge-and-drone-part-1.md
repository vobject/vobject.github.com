---
layout: post
title: Nightly Builds with Sourceforge and Drone (Part 1)
date: 2014-05-29 19:25:23
comments: true
tags: [Sourceforge, Drone, Nightly Builds, Continuous Integration]
categories:
---

This post lays the groundwork for some future tutorials on the topics of continuous integration, cross compilation and binary deployment. I will discuss each topic in greater detail but first give a high level overview about the scenario, the goal and how it is pursued. Some parameters of the scenario are very specific (e.g. using Sourceforge, Drone, and Mercurial), others may be adaptable to completely different problems.

In case the title seems too vague, here is a look at the original (more precise) title:
***Providing Nightly Builds of Windows Binaries though Cross Compilation on Ubuntu for Open Source Projects hosted on SourceForge using the Drone Coninuous Integration Service through a Proxy Project on BitBucket.***

# Scenario

1. Our existing project **ci-test** is hosted on [Sourceforge](https://sourceforge.net/).
2. **ci-test** uses [Mercurial](https://sourceforge.net/p/forge/documentation/Mercurial/) for revision control and gets new commits pushed every few days.
4. **ci-test** depends only on cross platform libraries.
5. **ci-test** is open source.

##### A word about SourceForge

Before we start: in my opinion SF is pretty much obsolete for new open source projects. DVCSs have taken over the OSS world in recent years and are not going away anytime soon. SF was too late to offer them as alternatives to their tool of choice Subversion. Even now, SF's integration of Git and Mercurial is laughable. SF does not support most cool features hosting providers such as [GitHub](https://github.com/) and [BitBucket](https://bitbucket.org/) introduced in the last years. On top of that, it is usually not supported by services (most notably continuous integration) building on top of modern hosters.

Having said that, SF still has a few important points in its favor: it offers users shell access to their account and their repositories. And it tolerates uploading large binary files for distribution, a use case that [other](https://github.com/blog/1302-goodbye-uploads) [services](https://help.github.com/articles/what-is-my-disk-quota) [still](https://help.github.com/articles/distributing-large-binaries) [struggle](http://stackoverflow.com/questions/10346370/what-is-the-best-practice-of-distributing-binaries-from-a-github-project) [with](https://github.com/blog/1547-release-your-software). This tutorial will make use of both of these features.

So, why SF? For the ease of argument, let us assume we are dealing with an existing project (you would not host a new project on SF, would ya?), and that migrating is not an option. In case the project is hosted on GitHub or BitBucket, some of the hurdles go away (e.g. proxy project). However, we would still use SF for deployment of binary artifacts.


# Goal

1. A build artifact should be created for new commits pushed to the SF repository.
2. The artifact should contain the project's binary for Windows.
3. The artifact should appear in the *Files* section on the SF project page.
4. We only want artifacts for the *default* branch (or *master* branch in git).
5. We do not want to spend money.

So the goal is to provide [nightly builds](http://en.wikipedia.org/wiki/Nightly_builds) for our project and make them publicly available. The thing about providing Windows binaries is to incorporate the cross compilation step into our journey. Since the continuous integration service builds on Linux, this is another challenge. Also, I have not yet come accross a free build service running Windows. Providing binaries for Linux would be much easier (and a bit pointless).


# Workflow

The development/distribution workflow we want looks like this:

1. *Developer A* pushes new commits to the default branch.
2. *Developer A* gets an email containing a link.
2.1 If *A* clicks the link, a nightly build is triggered and an new artifact appears a few minutes later.
2.2 If *A* ignores the email, nothing happens.

This approach is mostly automated but still gives the developer the choice not to provide a build artifact for his latest updates. I think this is a reasonable workflow for open source projects, whose default/master branch is not always as stable as it should be.


The following figure outlines the workflow and provides some more technical information about the actors and their relationships.

{% img /images/nightly-builds-with-sourceforge-and-drone/workflow.svg 'Workflow showing actors and relationships' %}

1. The developer pushes to the repo.
2. The developer receives an email with a link.
3. The developer clicks the link and starts a build.
4. The bash build script (running on Ubuntu 12.04) clones the repository from SF and sets up everything else it needs for building. It compiles the code and creates the artifacts (e.g. compress binary and config files into a single archive).
5. The script then uploads the artifacts into the designated SF project section. Settings up the authentification is a key task for making the process work and will be covered later.


# Setting things up

This section lists the major tasks to set up the workflow described. It serves as a high level tl;dr and should be enough for someone familiar with the technology and services involved. Everyone else will have to wait for the second part of this tutorial.

- Create an account for [BitBucket](https://bitbucket.org/) and [Drone](https://drone.io/) if you do not have one yet.
- Create a proxy project on BitBucket, e.g. **ci-test-nightly**. SF itself is not supported by Drone, so the purpose of this proxy project is just to be able to create a new build project on Drone.
- Login to Drone and create a 'New Project' based on **ci-nightly** from BitBucket.
- Enter 'default' into the *Settings -> Repository -> Branch Filter*
- Register the projects SSH key from *Settings -> Repository -> View Key* at your SF account.
- Create a folder 'NightlyBuilds' inside the project's files section via the SF shell.
- Copy the *Settings -> Repository -> Build Hook* link and create a Mercurial hook via the SF shell to send out an email containing this link to developers.
- Write the clone & build & upload script into the *Settings -> Commands* text box.


# Notes & Risks

- SourceForge took quite some time (hours) until the newly registered SSH key was accepted.
- Drone is still in Beta and may change considerably in the future. This may lead to alterations to the service that renders these instructions useless.
- Although I only talked about BitBucket and Drone, it should be possible to replace each of them with GitHub or Travic-CI. I have not done it, but do not see technical problems standing in the way.


# Future

The next part(s) will go into cross compilation on the Drone applience and how to configure SF through shell access.


# Reference

[Comparison of continuous integration software](http://en.wikipedia.org/wiki/Comparison_of_continuous_integration_software)
[CIaaS roundup: 11 hosted continuous integration services](http://blog.testnoir.com/ciaas-roundup-11-hosted-continuous-integration-services/)
[SourceForge: Release Files for Download (FRS)](http://sourceforge.net/apps/trac/sourceforge/wiki/Release%20files%20for%20download)
[SourceForge: Service quotas](https://sourceforge.net/apps/trac/sourceforge/wiki/Disk%20quotas)
[SourceForge: Trust issues with Mercurial project-based hgrc](https://sourceforge.net/apps/trac/sourceforge/ticket/5480)
[Drone Documentation](http://docs.drone.io/)
[Drone CI - Philly DevOps May 2014](http://vimeo.com/96136521)
