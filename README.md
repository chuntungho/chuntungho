## Initialization

> https://gohugo.io/installation/macos/
> 
> https://gohugo.io/getting-started/quick-start/

Install `hugo`: `brew install hugo`

Create a new project: `hugo new project quickstart` 

Create orphan branch `gh-pages`.

`git switch --orphan gh-pages`

Copy generated files to from `quickstart` to current folder.

Add customized theme `yassi`.

`git submodule add https://github.com/chuntungho/hugo-theme-yassi.git themes/yassi`

## Local debug

Checkout `gh-pages` branch and init submodule.

```bash
git checkout gh-pages
git submodule update --init --recursive
```

Startup: `hugo server`

## Deployment

Use github action to generate static pages and deploy to github pages.