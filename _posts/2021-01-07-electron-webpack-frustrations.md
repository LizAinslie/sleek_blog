---
layout: post
title: My Frustrations With Electron and Webpack
categories: [Rants]
---

So, a while back, I created a moderately popular app called MyRPC. (It's dead now) Essentially, it was a custom status application for Discord based on the RPC function of Discord. At the time of its creation, my knowledge was mostly of Node.js, so I opted to use Electron. The earliest versions of the code worked spectacularly, and the app was a  hit. However, I learned React at some point, and decided that MyRPC could use a touch of React. Bearing in mind that `create-react-app` was essentially the only way to do React at the time (unless you wanted to configure everything manually), I opted to use it. 

All was well, until I ran into some issues. (classic Node.js fairy-tale, right?) The Discord RPC worked fine, however what really hurt was the build process. I wanted to have cross-platform builds without having to know someone with a Mac and someone with a Linux installation, so I setup Travis CI and AppVeyor (for Windows because Travis CI's Windows support was sketchy at best). Here's where it got murky (and it's also why I keep my distance from most CI/CD pipelines). I wanted to do a few things with CI:

1. **Build an installer for each platform.** This wasn't too big of a problem after I got the build scripts fine-tuned.
2. **Create and tag a GitHub release on __tagged commits only__.** It wasn't too difficult to do this either. The hard parts were:
    - **Determining whether or not the commit was tagged.** Git isn't too nice about this. *Something, something, platform specific string parsing...*
    - **Creating only one release.** Travis and AppVeyor each had a different idea of how to generate a release name. And even if the names were the same, I still had problems getting AppVeyor to simply update the release if it already existed rather than just failing the job.
3. **Upload the built installers to the new release.** TL;DR, directory structures suck.
4. **Use the tagged commit's message as the release description.** Simply put, you should ***never*** attempt this. You will, by every guarantee, spend hours on end trying to get it to work properly and never succeed. Good luck.

### TL;DR
My frustration was mostly with the process. Between these steps, I was losing my mind:

1. Compile the UI with Webpack. Hope and pray to our lord and savior Satan that the Electron renderer module is accessible to the UI code.
2. Run the Electron app. Hope that it can find the Webpack output, and if it can't, start packing your bags for Brazil.
3. If all of the above works... well, here comes the fun part. Commit all your changes, tag the latest commit (if it's a release), and push that code.
4. Go fix the 1,000 issues that Travis CI and AppVeyor have when trying to build the app, and make damn sure they upload the installers to the same release.
5. Describe the release, because no matter how hard you try you can never seem to get Travis to pull the commit content and put it in the release description.

This should not have been so difficult, but alas: Electron.
