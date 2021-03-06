---
published: true
layout: post
tags: visual studio git
title: Free Git on Bitbucket with Visual Studio
---


![gwindows_logo](https://cloud.githubusercontent.com/assets/9366487/10289394/15e90efc-6b6c-11e5-9fa3-e4510cf375b8.png)
Today I'm sharing for my own memory banks how to add a new Visual Studio solution/project to Git source control. If you've heard of Git, you've probably heard of Github, which hosts roughly a zillion git repositories. One problem with Github (at least as of now) is that they don't offer private repositories for free, only public ones. If you don't want to pay for your little side projects to be hosted in a private repository, what is a poor dev to do? Simple, use [Bitbucket](https://bitbucket.org/) instead. With Bitbucket you can host private repositories with a free account.

So how do we host our git repository on Bitbucket for free? I'm glad I asked, so for me here are the steps (with too many missing screenshots, because I forgot to take them).

1. Login to [Bitbucket](https://bitbucket.org/)
2. Select Repositories...Create repository. You'll see this screen...
![bitbucket-create-repo](https://cloud.githubusercontent.com/assets/9366487/10289306/b21dd6c8-6b6b-11e5-9e67-50ac4eceec52.png)
3. Enter the repository details and click Create repository.
4. Back in Visual Studio, right-click on your solution and select Add solution to source control.
5. In the window that pops up, select Git.
6. In the Team Explorer window that comes up enter in an initial commit message.
7. On the button below it, select Commit and Push.
8. On the synchronization window that comes up enter in the bitbucket repository URL in this pattern...
https://{username}@bitbucket.org/{username}/{repository}.git
![visual-studio-enter-remote-repo](https://cloud.githubusercontent.com/assets/9366487/10289315/bb07915c-6b6b-11e5-9617-3a21eb269870.png)
9. Click Publish.
10. You're done. (Had to make it a nice even 10 steps).

Now, if you check in Bitbucket, you'll see the source control is now in the repository. A big thanks to my past self for writing this up from my future self (now present self).
