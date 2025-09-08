---
layout: post
title: The power of git bisect
date: 2025-09-08T18:00:00.000Z
published: true
---

Git bisect isn't a tool I need to use often but in the few cases where I did use it it saved me a ton of time. Recently I was tasked with fixing a regression in a module. The bug was a sorting algorithm not activating. There were no exceptions and no steps required to trigger the bug apart from opening the list. That's an annoying regression to track down since there aren't any clues to what is going on. Luckily since this sorting algorithm used to function normally it does mean we can track down when it broke.

Introducing git bisect. Git bisect is a tool for finding the commit that introduces a bug. It performs a binary search across commits where you can mark commits as good or bad. When you start a bisect you select a commit where you know that the code was working properly. Then you select a commit which you know is not functioning correctly which is most likely the most recent commit. Bisect will automatically check out the commit in the middle of those commits. You can then perform some testing to ensure that your code is functioning as expected, if it works as expected you can mark the currently checked out commit as good and you know that the regression started in a later commit. If it doesn't work as expected you can mark the commit as bad and you know that the regression started earlier. Repeat these steps a few times until you find the exact commit that caused the bug. If you don't squash your commits and keep commits small then you should have a pretty good indicator of what the problem is. Even on large projects with a lot of activity this shouldn't take too long since binary searches narrow the search are by half for each commit you check.

In the example that I started this post with I managed to track it down to a refactor commit of the date parse handling. Underlying code was expecting it to be a string containing the date, instead I was providing a Carbon (PHP extension for DateTime) instance. In most cases this instance was properly converted back to a string but not all methods handled this properly. Eventually tracking the origin of this regression and implementing a fix only took me a few minutes instead of the however long this would've taken if I had to manually test what had failed.

Another case where this provided an easy solution was during a problem where vite was unable to find the vite server in development environments. For the time being we had found a temporary solution that would fix it locally but only as long as we wouldn't restart the vite server. This problem had been plaguing us for a few weeks but no-one knew what the origin was of this bug which was partially due to a lack of attempts trying to solve this problem. This problem hadn't always existed so it's a perfect candidate for using git bisect. In just a few minutes I managed to track it down to a vite config change which I then quickly reverted to fix the bug.

For more information on how to use this amazing git feature you can check the [Git Bisect Documentation](https://git-scm.com/docs/git-bisect)
I personally enjoy using lazygit which has a nice gui built for git bisect. Simply press `b` to start bisect and mark commits as good and bad.
![Git Bisect Showcase](https://github.com/jesseduffield/lazygit/raw/assets/demo/bisect-compressed.gif)