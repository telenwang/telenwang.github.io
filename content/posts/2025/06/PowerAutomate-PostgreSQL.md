---
title: "Power Automate: Troubleshooting for connecting to local PostgreSQL (requires Npqsql 4.0.17.0 to be installed)"
date: 2025-06-18
draft: false
tags:
  - Power Automate
  - PostgreSQL
categories:
  - Troubleshooting
sitemap:
  priority: 0.7
  changefreq: weekly
author: "Telen Wang"
authorURL: "https://www.linkedin.com/in/telenwang"
---

Recently, when using Power Automate to connect to PostgreSQL 17 through a local gateway, it prompted me to install Npqsql 4.0.17.0 or earlier.

![](/'Screenshot2025-06-01125642.png)

I installed 4.0.17.0 as prompted, but still got the same error. I also replaced all the DLL files under the local gateway program. Nothing helped. 

The similar cases can be traced several years ago, most of them were for Power BI topic with PostgreSQL. 

I checked the official website:

> PostgreSQL connector requires NPGSQL [ADO.NET](http://ado.net/) provider 4.0.10 to be installed.

Well, that's inconsistency. 

![](/'Screenshot2025-06-01130937.png)

Got it work

![](/Screenshot2025-06-01131035.png)

I hope this helps others who encounter similar errors.