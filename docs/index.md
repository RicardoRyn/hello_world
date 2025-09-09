# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

- `mkdocs new [dir-name]` - Create a new project.
- `mkdocs serve` - Start the live-reloading docs server.
- `mkdocs build` - Build the documentation site.
- `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## 测试

### 测试1

更改内容之后，`gh-pages`分支是否会保留所有commits？
并没有，`gh-pages`分支只保留最新的commit

---

再次更改后，`gh-pages`分支是否会保留所有commits？
并没有，`gh-pages`分支只保留最新的commit。
确实，`gh-pages`分支只保留最新的commit。
那为什么`plotfig`的`gh-pages`中有那么多commits

---

测试在ci文件中使用`uv run mkdocs gh-pages --force`，是否会保留所有的commits？
不会，`gh-pages`分支只保留最新的commit

### 测试2

当前网页版本应: `1.9.3`

---

网页版本控制是否生效？
没有，确实需要修改`website_deploy.yml`文件

---

修改完`.github/workflows/website_deploy.yml`，是否生效？
有用，还是要改`.github/workflows/website_deploy.yml`
