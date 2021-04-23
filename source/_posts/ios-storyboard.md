---
title: ios-storyboard
date: 2018-10-27 15:37:06
tags:
- ios
---

### 1. Storyboard的Entry Point
选择一个ViewController,在Attributes Inspector中勾选`Is Initial View Controller`。

### 2. Storyboard的导航
有两个导航`Navigation Controller`和`Tab Bar Controller`,这两个在Editor-Embed In->Navigation Controller/Tab Bar Controller.加上去之后，根组件就会变成这两个。`self.navigationController`才会有值，不然是nil。

<!-- more -->

### 3.不用StoryBoard
``` Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    // First ViewController to show
    ViewController *firstVC = [[ViewController alloc] init];
    
    // Create a UINavigationController to navigate, this is root navigation controller
    // Each page navigatted from it will have slef.navigationController
    UINavigationController *naviController = [[UINavigationController alloc] initWithRootViewController:firstVC];

    // Set window, if we don't use StoryBoard, need create window by ourself.
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    // Set window rootViewController, this is the entry point like StoryBoard
    self.window.rootViewController = naviController;
    
    // Set window backgroundColor
    self.window.backgroundColor = [UIColor whiteColor];
    
    // Show the window
    [self.window makeKeyAndVisible];

    return YES;
}
```
https://stackoverflow.com/questions/7767049/self-navigationcontroller-is-null

### 4. First Responder
页面的第一个响应者，比如Button, Text

### 5. 使用tips
#### 5.1 打开控件库
两种方式：
1. 快捷键`Command + Shift + L`
2. View->Show Library

#### 5.2 连接inlet, outlet
界面Split代码和设计视图，鼠标左击控件，拉入代码视图的声明部分是inlet,方法拉入实现部分是outlet

#### 5.3 跳转
Ctrl+鼠标左键 或者 鼠标右键 控件，Drag 到目标ViewController, 可以选择Segue(就是路由跳转)，选择其中一个就可以跳转了。