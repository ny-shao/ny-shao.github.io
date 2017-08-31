---
layout: post
title: Updated to GHOST 0.60!
date: '2015-04-22 00:01:26'
tags:
- ghost-tag
- blog
- openshift
---

After suffering several months notifications of "update to new version", I just updated the Ghost from 0.50 to 0.60, skipping 0.51 and 0.52.

The update is quite easy, thanks to [fuzzmz](https://ghost.org/fuzzmz) on Ghost forum! I just followed [this post](https://ghost.org/forum/installation/16533-resolved-upgrading-ghost-0-5-to-0-5-2-on-openshift/):

1. Set up a new installation using: `rhc app create test nodejs-0.10 mysql-5.1 --env NODE_ENV=production --from-code https://github.com/openshift-quickstart/openshift-ghost-mysql-quickstart.git`
2. Go to URL/ghost and setup the admin user.
3. Download latest Ghost archive - [https://ghost.org/zip/ghost-0.5.2.zip](https://ghost.org/zip/ghost-0.5.2.zip)
4. Extract archive to somewhere :)
5. cd to the test folder
6. delete `index.js` and `package.json`
7. delete the `core` folder
8. delete the `content/themes/casper` folder
9. copy `index.js`, `package.json`, the `core` and `content/themes/casper` folders from where you extracted the archive to the test folder (the git repo created when I created the OpenShift app).
10. modify the new `package.json` (the one in the test folder) with `"main": "index.js"` instead of `"main": "./core/index"`
11. `git add --all` .
12. `git commit -am "update to ghost v0.5.2"`
13. `git push origin master`
That's it. You should now be running the latest version of Ghost.

And openshift is powerful, just restarted, compiled and deployed automatically.