---
layout: android
title: Bundle
date: 2021-05-27 11:00:54
tags:
---

### Launch Instant App
Instant App depends on Play Dynamic Delivery, Dynamic Delivery depends on Dynamic Features.

Developers submit .aab file to GooglePlay, then Goole Play use tool `bundletool` to extract the apks from this bundle file. Note: apks is a file format means apk set, it's not equal to apk.

We cannot use `adb install` to install .apks archive, we need a new tool `ia` coming from Google to install .apks file. This process also happens when users run instant apps published on GoolgePlay.

Actually, we needn't to use bundletool & ia to launch instant app now, the Android Studio already can launch the intant app conveniently.

<!--more-->
