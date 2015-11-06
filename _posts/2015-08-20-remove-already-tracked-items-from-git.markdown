---
layout: post
title: Remove Already Tracked Items from Git
permalink: remove-already-tracked-items-from-git
date: '2015-08-20 17:25:27'
tags: [Git, Development]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
---

After wrestling with a .NET app that wasn't excluding the "packages" directory from the repo, I realized I had missed a crucial and obvious step... **I forgot to commit the change to finalize the removal**.  Talk about one of those "you forgot a comma" moments...

If you find yourself in the same boat - here is how you do it.

To remove the directory:

```
git rm -cache -r directory/path/
```

This will recurse through the directory and pull all of its contents out of the history.

To finalize this, ensure that the directory is listed in your `.gitignore` file and commit the change:

```
git commit -m "Removed directory from tracking and added to .gitignore"
```

And that's it!

`</brainfart>`
