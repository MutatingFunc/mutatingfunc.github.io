---
title: "Getting started with Git - cloud storage for collaboration"
date: 2024-XX-XXTXX:XX:00-00:00
draft: true
tags: ["Git"]
categories: ["Workflow"]
---

In this article:
• What is a commit?
• Push vs Pull
• Pull vs Fetch
• Git branches
• ‘Feature’ branches
• Merge vs Rebase
• Squash, Fixup
• Interactive rebase

# Basics

Cloud storage is great - for one person. But try to have two people collaborate on a big writing project, and things get complicated quickly.

If you were to try collaborating with a regular cloud storage, the first issue you'd face is conflicts - you change one file, another person changes their copy, and boom! At best, you get an opaque warning asking you to choose one or both. At worst, their change overwrites yours, and your work is lost completely.

Git is more complicated than cloud storage. This is because it seeks to make conflicts understandable, managable, and less frequent. It does this by splitting changes into small packets, or ‘commits’, containing a set of additions, deletions, or edits to files. It's up to the user to create define how many changes go into each commit.

By consequence, git has another nifty trick - built-in file versioning. Each commit builds upon the last, letting you rewind a file right back to when it was first created.

So that's the concept. But how does it work?

# Getting started

The first thing you probably want is, well, cloud storage. But while regular cloud storage will blindly mirror every file to every computer, *git* cloud storage providers will use the system we just described.

Github is the main one by far, super reliable and free. GitLab and BitBucket are more options. Just like how iCloud, OneDrive, and Google Drive all offer broadly the same service with different technical details, these providers are also broadly equivalent.

With git, you'll get multiple spaces to store things in, rather than just the one. These are called ‘repositories’ because ‘store’ just sounded too simple and user friendly.

You can create a repository via Github and ‘clone’ it to set up sync to your computer, or if you want one offline you can ‘initialise’ one within a folder. I'd recommend using a Git app, like Fork, to get started - the command line doesn't help you visualise things.

# Making a commit

![Name](1%20Commits.jpeg)
