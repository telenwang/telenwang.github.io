---
title: "How to change PCF local port number(8181)"
date: 2025-08-03
draft: false
tags:
  - Power App
  - PCF
categories:
  - Customization
sitemap:
  priority: 0.7
  changefreq: weekly
author: "Telen Wang"
authorURL: "https://blog.nelettech.com"
---

You might have noticed that when you tested your PCF component locally, npm always used port number:8181 as default. 

Is that possible to change a different one ? and How?

```cmd
[start] Initializing...
[start] Validating manifest...
[start] Validating control...
[start] Generating manifest types...
...
Starting control harness...
[Browsersync] Access URLs:
 ----------------------------
 Local: http://localhost:8181
 ----------------------------
[Browsersync] Serving files from: C:\projects\LinearInput\out\controls\LinearInputControl
[Browsersync] Watching files...
```

The short answer is Yes if that really bothers you.

In your PCF control project folder, we can check package.json file. And it tells two important js in the file:

1. pcf-scripts. Located '\node_modules\pcf-scripts\bin\pcf-scripts.js'
2. pcf-start. Located '\\node_modules\pcf-start\bin\pcf-start.js'

When you open pcf-start.js, you will find port:8181 is defined there. 

Just change the port number with a different one, for example 8080

```js
function RunTask() {
    ...
    // Start server
    bs.init({
        online: false,
        port: 8181, //Change the port number here,8080 for example.
        reloadDelay: 1000,
        server: {
            baseDir: ...
            routes: {
                ...
            },
        },
        ui: false,
        watch: true, // node_modules ignored automatically
    });
    return 200;
}
```

After change, save the file and try:

![image-20250803222454771](/image-20250803222454771.png)

Enjoy.

