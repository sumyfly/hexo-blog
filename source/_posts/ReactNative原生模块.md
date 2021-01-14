---
title: ReactNative原生模块
date: 2018-01-12 01:50:37
tags:
  - react-native
  - 原生模块
---

### 1.原生模块
#### 1.1Anroid
有三个`NativeModule`, `JavaScriptModule`, `createViewManagers`

#### 2.IOS
应该有三个，导出模块或者原生View，都是用`RCT_EXPORT_MODULE`

### 2.Android原生View的显示bug
Android上，我发现一个View始终不显示，后来发现原因，是原生的View需要重绘，调用自己的requestLayout,再调用measureLayout

这个bug我找了好久，最后没办法试了一下，居然解决了。

``` java
private final Runnable mLayoutRunnable = new Runnable() {
    @Override
    public void run() {
      measure(
          MeasureSpec.makeMeasureSpec(getWidth(), MeasureSpec.EXACTLY),
          MeasureSpec.makeMeasureSpec(getHeight(), MeasureSpec.EXACTLY));
      layout(getLeft(), getTop(), getRight(), getBottom());
    }
  };

  @Override
  public void requestLayout() {
    super.requestLayout();

    // The toolbar relies on a measure + layout pass happening after it calls requestLayout().
    // Without this, certain calls (e.g. setLogo) only take effect after a second invalidation.
    post(mLayoutRunnable);
  }
```
1.  [使用原生自定义View，setState刷新界面后UI不能正常显示](http://blog.csdn.net/it_talk/article/details/56844151)
2. [github issue](https://github.com/facebook/react-native/issues/4990)