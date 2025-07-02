---
title: "How to handle empty object for ParseJSON in Power Automate"
date: 2025-07-02
draft: false
tags:
  - Power Automate
  - Parse JSON
categories:
  - Customization
sitemap:
  priority: 0.7
  changefreq: weekly
author: "Telen Wang"
authorURL: "https://blog.nelettech.com"
---

Occasionally, while building a Power Automate flow, you’ll need your **`ParseJSON`** action to accept an *empty* value—whether that value is an object (`{}`), array (`[]`), or string (`""`). By default, ParseJSON balks at these inputs. So how do we get around that limitation?

In this article, I’ll explore a simple, reliable approach that lets your flow handle empty JSON values gracefully.

### Create Manually trigger a flow.

### Add Initialize variable action.

Name: theEmpty.

Type: String/Array/Object, I choose Array for first try.

![image-20250702204115710](/image-20250702204115710.png)

```json
{
    "type": "InitializeVariable",
        "inputs": {
        "variables": [
            {
                "name": "theEmpty",
                "type": "array"
            }
        ]
    },
    "runAfter": { }
}
```

### Then add a Compose.

Input :

```json
json(
    if (
    or(
        empty(variables('theEmpty')),
        equal(variables('theEmpty'), '')
    )
    , '[]'
    , 'test' // replace this with the actual json output.
)
  )
```

This make sure if any empty or null input, it will be initialized as []

### Last add ParseJSON action.

The content = outputs('Compose')

I use minimal schema for testing. Here any valid schema will work.

![image-20250702205445671](/image-20250702205445671.png)

Handling empty JSON values in Power Automate boils down to one small adjustment—but it removes a big source of frustration. By letting **ParseJSON** accept `{}`, `[]`, or `""`, your flows stay resilient and error-free no matter what data comes through. Give the technique a try in your next project, and feel free to share any twists or improvements you discover along the way—there’s always more to automate!