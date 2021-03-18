---
title: Google
date: 2021-03-18 14:41:16
tags:
---

### 1. Firebase Admin SDK
按照文档集成之后，发现查询DynamicLinks的数据返回`403`，那么就需要到GCP(Google Cloud Platform)中检查用户email的权限，这个权限根具体内容相关，默认的Firebase Admin SDK的权限只有两个，需要什么服务自己加上。

1.Find the client_email in the service account file
2.Go to GCP > select your Firebase project > click IAM & admin > click IAM https://console.cloud.google.com/iam-admin/iam?project=_
3.Make sure the IAM row for the client_email in step 1 has the Firebase App Distribution Admin role, or add it if it is missing. Check by clicking the "edit" pencil icon to the right-side of the row and a panel will show the current roles. If Firebase App Distribution Admin isn't there, press "+ ADD ANOTHER ROLE" and include it.

https://stackoverflow.com/questions/62385911/firebase-app-distribution-failed-to-fetch-app-information-403-the-caller-do