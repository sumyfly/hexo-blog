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

### 3.不用StoryBoard
``` oc
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
