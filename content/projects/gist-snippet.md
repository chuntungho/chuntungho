+++
title = "Gist Snippet"
date = 2024-01-20
summary = "An IntelliJ plugin of code snippet tool based on GitHub Gist."
featured = true
weight = 3
tags = ["idea","gist"]
categories = ["Plugin"]
url = "gist-snippet"
main_image = "/images/gist.svg"
+++


[Plugin homepage](https://plugins.jetbrains.com/plugin/13587-gist-snippet/) \| [Source code](https://github.com/chuntungho/gist-snippet)

A code snippet tool based on GitHub Gist, that provides with a feature to fetch own or starred gists of GitHub accounts. It depends on built-in GitHub plugin which should be enabled.

Usage {#usage}
--------------

1. Right click on editor, select "Insert Gist..." menu item at bottom to use the plugin.  
   ![1](/upload/2021/07/1-d113230a1962479d915227a52899dbef.png)

2. Select "Own" or "Starred" item at left top to load your own or starred gists.  
   ![2](/upload/2021/07/2-85d075929758416fbeb3c524ed3f3c13.png)

3. Select gist file item to preview file content at the right，click "Insert" button to insert selected text or whole file content into editor.  
   ![3](/upload/2021/07/3-cfa194149769463aada4eaec282fe6d0.png)

PS {#ps}
--------

If no GitHub account exists, please click "Add GitHub Account" to add your account, or seach "github" in IDEA preference dialog to configure GitHub accounts.  
![4-1](/upload/2021/07/4-1-216e578f23ac401cb13909ede3dbe6ec.png)  
![4-2](/upload/2021/07/4-2-c288964b8be146279b957d525a6021da.png)

You can select "Create Gist..." of editor popup menu to create code snippet under your account，or star your favorite gists on GitHub website.  
![5](/upload/2021/07/5-062ab183be374502bdbe627f69ded029.png)

Gist description will be parsed to extra fields in this format: `[tag1] [tag2] #title# description`