---
title: "Getting Started with Scala"
date: "2012-09-22"
categories: 
  - "technology"
tags: 
  - "intellij"
  - "sbt"
  - "scala"
---

We're going to put together a working Scala system on MacOS. We're going to need a few things:

- [SBT](http://www.scala-sbt.org/) - this is our build system and dependency management
- [IntelliJ](http://confluence.jetbrains.net/display/IDEADEV/IDEA+12+EAP) - our IDE
- [Scala](http://www.scala-lang.org)
- A few Scala plugins that we'll pull in via SBT:
    - FunSuite - our testing system
    - gen-idea - a system to generate IntelliJ projects from sbt projects.

## Download and Install Mac Packages

First start the download of the [IntelliJ](http://confluence.jetbrains.net/display/IDEADEV/IDEA+12+EAP) early access program, as it's going to take the longest to download.

Visit [http://www.scala-lang.org/downloads](http://www.scala-lang.org/downloads) and find a distribution. Download it and install it. Once you're done, you can install sbt via brew like this:

**brew install sbt**

Once SBT, Scala, and IntelliJ are installed, we'll get started configuring the IntelliJ Scala and SBT plugins. The EAP version is currently called LEDA UI, not IntelliJ as you might expect. Start LEDA IU, then click "Open Plugin Manager".

[![](http://paymentnetworks.files.wordpress.com/2012/09/shot1-plugin-manager-button.png?w=300 "shot1-plugin-manager-button")](http://paymentnetworks.files.wordpress.com/2012/09/shot1-plugin-manager-button.png)

[![](http://paymentnetworks.files.wordpress.com/2012/09/shot2-scala-plugins.png?w=300 "shot2-scala-plugins")](http://paymentnetworks.files.wordpress.com/2012/09/shot2-scala-plugins.png)

Once the plugin manager loads, we'll click "Browse Repositories", then search for Scala. In the results, click the check mark to install both SBT and Scala. These plugins don't work in IntelliJ 11, it's important that you use the latest EAP. The plugins will download and install. Close "LEDA IU" once they're done installing. We'll use SBT to create our project.

## Create a Project in SBT

Start a terminal. Make a directory, then run sbt to create a new project.

~/projects$ **mkdir scala-project**
~/projects$ **cd scala-project/**
~/projects/scala-project$ **sbt**
\[info\] Set current project to default-d4c499 (in build file:/Users/Thoughtworks/projects/scala-project/)
>**test**
\[info\] Updating {file:/Users/Thoughtworks/projects/scala-project/}default-d4c499...
\[info\] Resolving org.scala-lang#scala-library;2.9.1 ...
\[info\] Done updating.
\[info\] No tests to run for test:test
\[success\] Total time: 1 s, completed Sep 21, 2012 9:16:42 PM
>

Next, create and edit a build.sbt file in the current directory and change the name of your project to something you like better. Here's an example build.sbt file to start from. Make sure the scalaVersion listed here is the version you installed.

~/projects/scala-project/build.sbt:

name := "awesome-project"

version := "0.1"

scalaVersion := "2.9.1"

libraryDependencies ++= Seq(
 "org.scalatest" %% "scalatest" % "1.6.1" % "test"
)

Then run sbt again. If you run test (or most anything else), it'll pick up the new requirements, download and install them for us.

~/projects/scala-project$ **vi build.sbt**
~/projects/scala-project$ **sbt**
\[info\] Set current project to awesome-project (in build file:/Users/Thoughtworks/projects/scala-project/)
> **test**
\[info\] Updating {file:/Users/Youruser/projects/scala-project/}default-d4c499...
\[info\] Resolving org.scala-lang#scala-library;2.9.1 ...
\[info\] Resolving org.scalatest#scalatest\_2.9.1;1.6.1 ...
\[info\] Done updating.
\[info\] No tests to run for test:test
\[success\] Total time: 1 s, completed Sep 21, 2012 9:27:56 PM

Now we need to create a plugins folder under project, and create a build.sbt file there.

~/projects/scala-project$ **mkdir -p project/plugins**
~/projects/scala-project$ **cd project/**
~/projects/scala-project/project$ **cd plugins/**
~/projects/scala-project/project/plugins$ **ls**
~/projects/scala-project/project/plugins$ **vim build.sbt**
~/projects/scala-project/project/plugins$

The build.sbt file in the plugins folder needs to have one line in it:

addSbtPlugin("com.github.mpeltonen" % "sbt-idea" % "1.1.0")

Then we'll change directories back to the project root and run sbt again. Once it starts we should have access to the gen-idea plugin that makes IntelliJ projects for us.

~/projects/scala-project/project/plugins$ **cd ..**
~/projects/scala-project/project$ **cd ..**
~/projects/scala-project$ **sbt**
\[warn\] Using project/plugins/ is deprecated for plugin configuration (/Users/Thoughtworks/projects/scala-project/project/plugins).
...
\[info\] Resolving org.scala-sbt#precompiled-2\_9\_2;0.11.3 ...
\[info\] Done updating.
\[info\] Set current project to awesome-project (in build file:/Users/Thoughtworks/projects/scala-project/)
> **test**
\[info\] No tests to run for test:test
\[success\] Total time: 0 s, completed Sep 21, 2012 9:40:31 PM
>

Now we're ready to generate an IntelliJ plugin. Just run the gen-idea task in sbt.

\[success\] Total time: 0 s, completed Sep 21, 2012 9:40:31 PM
> **gen-idea**
\[info\] Trying to create an Idea module default-d4c499
\[info\] Resolving org.scala-lang#scala-library;2.9.1 ...
\[info\] Resolving org.scalatest#scalatest\_2.9.1;1.6.1 ...
\[info\] Excluding folder target
\[info\] Created /Users/Thoughtworks/projects/scala-project/.idea/IdeaProject.iml
\[info\] Created /Users/Thoughtworks/projects/scala-project/.idea
\[info\] Excluding folder /Users/Thoughtworks/projects/scala-project/target
\[info\] Created /Users/Thoughtworks/projects/scala-project/.idea\_modules/default-d4c499.iml
\[info\] Created /Users/Thoughtworks/projects/scala-project/.idea\_modules/default-d4c499-build.iml
> **exit**
~/projects/scala-project$ **mkdir -p src/main/scala**
~/projects/scala-project$ **mkdir -p src/test/scala**
~/projects/scala-project$

Now we're ready to open up our project in IntelliJ. More in the next [post](http://paymentnetworks.wordpress.com/2012/09/21/getting-started-with-scala-part-two/)!
