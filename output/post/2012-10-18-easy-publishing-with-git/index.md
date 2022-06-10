---
title: "Easy Publishing With Git"
date: "2012-10-18"
categories: 
  - "technology"
tags: 
  - "asciidoc"
  - "git"
  - "git-scribe"
---

A [colleague of mine](http://www.grahambrooks.com/blog/) showed me something really cool: a toolchain that helps different people work together on a project involving documentation. With one simple command, the content can be rendered in to a website. As a nice bonus, the site will not only have a table of contents and contain the content you wrote but also link to PDF, MOBI (Kindle), and a single page HTML file.

To make this work, you want a tool called [git-scribe](https://github.com/schacon/git-scribe) and a git repository. All team members will be writing in different text files and pushing changes back, possibly to a central repository. Strictly speaking, git isn't required - git-scribe can be used on any directory with the right files in it, but git really helps with the collaboration.

Secondly you need asciidoc for formatting. It's simple like markdown, but more extensible and with more features. If you're familiar with the markdown concept, an [asciidoc cheatsheet](http://powerman.name/doc/asciidoc) should get you started very quickly. There's a great [TextMate bundle](https://github.com/zuckschwerdt/asciidoc.tmbundle) that allows you to preview your asciidoc while you're writing it\*.

Once you're done assembling "chapters" (I use one file per chapter), you create a book file to contain them all using asciidoc include statements like this:

Book Title
==========
:Author: Your Name
:doctype: book
:lang: en
include::chapter-one.asc\[\]
include::chapter-two.asc\[\]
include::chapter-three.asc\[\]

Simply run `git-scribe gen` in the root of your repository. It will create an output directory with a PDF file, a mobi file, and several html files. It even builds a title page and a table of contents for you. If git-scribe complains that it can't create all of the output targets, run `git-scribe check` and it will check your system for the various components it needs - most of them can be installed via `gem` or `homebrew`.   You're done!

_\* As a note, the instructions on the page describing the textmate bundle for asciidoc missed one step: if you have asciidoc installed in /usr/local/bin like I do, you need to go in to TextMate's preferences, advanced, shell variables and add /usr/local/bin to the beginning of the path; then go to bundles, bundle editor, and hit reload bundles._
