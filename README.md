Nexus on OpenShift
==================

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
rhc app-create nexus diy-0.1 --from-code https://github.com/juliogonzalez/sonatype-nexus-openshift.git
```
Or at the web interface:

[https://openshift.redhat.com/app/console/applications](https://openshift.redhat.com/app/console/applications)

And specifying https://github.com/juliogonzalez/sonatype-nexus-openshift.git at *Source Code*. In this case you can specify a tag if you do not want to install the current version.

Wait until the app is deployed
------------------------------

After some time, your Nexus will be availabe at:

[https://nexus-$yournamespace.rhcloud.com/](https://nexus-$yournamespace.rhcloud.com/)

The default nexus user is admin/admin123. Remember to change it!

How to upgrade
==============

First, make a backup of the *${HOME}/app-root/data/sonatype-work* directory, just in case, and read Nexus relase notes, available at their website.

Grab the GIT URL for your application, from the web console or using CLI, and clone the repository.

Switch to your local cloned repository, and add a new remote pointing to my repository:
```
git remote add upstream https://github.com/juliogonzalez/sonatype-nexus-openshift.git
``` 
Pull the changes, my changes will prevail over yours:
```
git pull -s recursive -X theirs upstream master
```
And, finally, push to your instance:
```
git push
```
The upgrade will be ready in a few minutes.

When it is completed and you can verify everything is correct, you can remove the old nexus folder from the server, located at *${HOME}/app-root/data/*
