# 操作说明

所有网页数据都在 `docs` 目录下，文章数据都是 Markdown 格式，html 格式的文件都是 jekyll 自动生成的

## 创建博客

在 `_post` 目录下创建名为 `年-月-日-标题.md` 的文件

## 创建草稿

在 `_drafts` 目录下创建名为 `年-月-日-标题.md` 的文件，编译的时候要使用 `--drafts` 参数

## 静态文件的使用

放到 `assets` 目录下，然后在博客中通过类似 `![有帮助的截图]({{ site.url }}/assets/screenshot.jpg)` 或者 `[下载 PDF]({{ site.url }}/assets/mydoc.pdf)` 的方法引用

## 本地编译查看

`bundle install`

`bundle exec jekyll serve`

## 上传到 GitHub

```
git add .
git commit -m 'xxx'
git push -u origin gh-pages
```

## 参考资料

- [Jekyll 中文站](https://jekyllcn.com/docs/home/)
- [使用 Jekyll 创建 GitHub Pages 站点](https://docs.github.com/zh/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)
