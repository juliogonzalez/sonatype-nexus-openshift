[![Build Status](https://jenkins-juliogonzalez.rhcloud.com/buildStatus/icon?job=sonatype-nexus2-openshift-build)](https://jenkins-juliogonzalez.rhcloud.com/job/sonatype-nexus2-openshift-build)

**IMPORTANT:** These are instructions for Nexus 2.x. See [master branch](https://github.com/juliogonzalez/sonatype-nexus-openshift/tree/master) to deploy Nexus 3.x 

Nexus 2.x on OpenShift
======================

This repository will deploy [Sonatype Nexus](http://www.sonatype.org/nexus/) to [Openshift](https://www.openshift.com/).

It is a variation of other solutions that use Tomcat (not recommended anymore as Nexus War is [deprecated](https://support.sonatype.com/hc/en-us/articles/213465668-Where-is-the-Nexus-OSS-war-file-)), archiving binaries at the repository itself, or starting/stopping services without control.

In this case the official jetty bundle distribution is used, no binaries are archived at the repository, and start/stop are controlled and report errors when needed.

No configuracion files are changed, as all configuration is made using environment variables. The only sensible difference is that Nexus is started calling directly to jetty to avoid using jsw, as jsw tries to bind to some TCP ports in a way not allowed by OpenShift.

I decided not to upgrade jsw (and setup wrapper.backend.type=PIPE) to use only the official Sonatype solution.

How to deploy
=============

Create an OpenShift account
---------------------------

If you do not have an OpenShift account, you can create one at http://openshift.redhat.com/

Create a DIY application
------------------------

Second step is creating a new DIY (Do It Yourself) application.

You can do it by using the CLI and running:
```
rhc app-create nexus diy-0.1 --from-code https://github.com/juliogonzalez/sonatype-nexus-openshift.git#**REF**
```
Replace **REF** with **nexus-2.x** if you want latest 2.x version available, or use a Nexus 2.x tag

Or at the web interface:

[https://openshift.redhat.com/app/console/applications](https://openshift.redhat.com/app/console/applications)

And specifying https://github.com/juliogonzalez/sonatype-nexus-openshift.git at *Source Code*.

At *Branch/Tag* field specify **nexus-2.x** branch or a Nexus 2.x tag.

Wait until the app is deployed
------------------------------

After some time, your Nexus will be availabe at:

[https://$yourcartridgename-$yournamespace.rhcloud.com/](https://$yourcartridgename-$yournamespace.rhcloud.com/)

The default nexus user is admin/admin123. Remember to change it!

How to upgrade
==============

First, make a backup of the *${HOME}/app-root/data/sonatype-work* directory, just in case, and read Nexus relase notes, available at their website.

Grab the GIT URL for your application, from the web console or using CLI, and clone the repository.

Switch to your local cloned repository, and add a new remote pointing to my repository:
```
git remote add upstream https://github.com/juliogonzalez/sonatype-nexus-openshift.git
``` 
Pull the changes, my changes will prevail over yours. Be sure you replace **REF** with **nexus-2.x** for latest 2.x version, or with a Nexus 2.x tag:
```
git pull -s recursive -X theirs upstream REF
```
And, finally, push to your instance:
```
git push
```
The upgrade will be ready in a few minutes.

When it is completed and you can verify everything is correct, you can remove the old nexus folder from the server, located at *${HOME}/app-root/data/*
