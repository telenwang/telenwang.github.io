---
title: "How to get files from Azure blob to SharePoint via Power Automate"
date: 2025-07-10
draft: false
tags:
  - Power Automate
  - Azure Blob
categories:
  - Sharepoint
sitemap:
  priority: 0.7
  changefreq: weekly
author: "Telen Wang"
authorURL: "https://blog.nelettech.com"
---

Sometimes you may want to get file from Azure Blob to your SharePoint Document library.

I will demonstrate a minimal Power Automate flow to achieve that. 

Before you take action, make sure you have Azure blob account and proper access right.

Assume I have one root container, and two sub folders within it.

## Initialize variables for Blob account and the root container.

![image-20250710154141347](/image-20250710154141347.png)

![image-20250710154159603](/image-20250710154159603.png)

## Add Lists blobs (V2).

![image-20250710154231293](/image-20250710154231293.png)

## Add For each.

Select an output from previous steps = `@outputs('Lists_blobs_(V2)')?['body/value']`.

Inside of the loop:

### Add another Lists blobs (V2), for each sub container/Folder from previous List blobs.

Folder:  `@items('For_each_folder')?['Path']`

![image-20250710154358333](/image-20250710154358333.png)

### Add another For each, which will loop each file from the Sub containers/folders.

Select an output from previous steps: `outputs('Lists_blobs_(V2)_for_each_one')?['body/value']`

![image-20250710154745535](/image-20250710154745535.png)

#### Add Get blob content using path (V2) within the loop

Blob path: `@items('For_each_file')?['Path']`

![image-20250710154845728](/image-20250710154845728.png)

#### Add create file for the SharePoint.

Specify the Folder Path which is from your SharePoint.

File Name = `@items('For_each_file')?['DisplayName']`

File Content = `@body('Get_blob_content_using_path_(V2)')`

![image-20250710154938247](/image-20250710154938247.png)

Whole flow screen shot.

![image-20250710161459300](/image-20250710161459300.png)

As you can see, it will be quite useful when moving content between SharePoint and Azure Blob.

