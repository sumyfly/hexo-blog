---
title: react-web
date: 2017-09-08 12:17:23
tags:
  - react
  - web
---

### 1.使用svg
#### 1.1  image 
``` javascript
import backIcon from '../assets/svg/back.svg'    
<img src={backIcon} />
```

#### 1.2 div background
``` javascript
import backIcon from '../assets/svg/back.svg'    
<div style={{
  background: 'url(' + backIcon + ') no-repeat 50% 50%',
  width: width,
  height: height,
  ...style
}} />
```
#### 1.3 div background-image
Ï``` javascript
import backIcon from '../assets/svg/back.svg'    
<div style={{
  backgroundImage: 'url(' + source + ')',
  width: width,
  height: height,
  ...style
}} />
//这个是渐变色背景
<div style={{
  backgroundImage: 'linear-gradient(to bottom, #26a9ff 0%, #324dff 100%)',
  width: width,
  height: height,
  ...style
}} />
```

