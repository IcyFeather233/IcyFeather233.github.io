## Installation

Run `pip install mkdocs` to install.

Check if installed:

`mkdocs --version`

## Quick Start

### Local Work

Change directory to your workspace, and run `mkdocs new dir_name` to init a mkdocs dir.

Serve on localhost: `mkdocs serve`

Build sites: `mkdocs build`

### Remote Work

First, create a repo on Github manually.

Then run as follows:

```
git init
git remote add origin https://github.com/your_username/your_repo.git
# run in your mkdocs dir, this will automatically run git push to gh-pages branch
mkdocs gh-deploy
```

Don't forget to set your repo githubpage:

Settings-Pages-Source(Deploy from a branch)-Branch(gh-pages, /(root))

### Update

Github Page:

```
mkdocs build
mkdocs gh-deploy
```

Source code:

```
git add .
git commit -m "change xxx"
git push --set-upstream origin source
```

## References

- [Material for MkDocs(Official)](https://squidfunk.github.io/mkdocs-material)
- [so easy！从头教你用mkdocs构建个人博客系统~](https://blog.csdn.net/qq_41261251/article/details/116021097)

Thanks Above