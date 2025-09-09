# HELLO_WORLD

这是一个用来学习、测试各种功能的仓库，顺带记录一些学习笔记

## release-please

release-please是一个可以自动化的版本管理工具，可以帮助你管理版本，并且在每次发布时自动化的生成你的CHANGELOG

初创仓库时，需要：

- `release-please-config.json`: 用来配置release-please
- `.release-please-manifest.json`: 用来记录当前版本，初创为“{}”，否则,例如“{".":"1.6.1"}”
- `.github/workflows/release.yml`: 用来触发github actions
- 每个仓库需要配置 RELEASE_PLEASE_ACCESS_TOKEN 密钥

## mkdocs

### 安装与部署

```bash
uv add --dev mkdocs-material # 安装mkdocs-material
uv run mkdocs new .          # 为仓库初始化mkdocs
```

在`mkdocs.yml`中添加:

```yml
site_url: https://ricardoryn.github.io/hello_world # 你的域名
theme:
  name: material
```

创建`.github/workflows/website_deploy.yml`:

```yml
name: Website Deploy
on:
  push:
    branches:
      - master
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: ~/.cache
          restore-keys: |
            mkdocs-material-
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

将目前更改push到 main 分支，静态站点将自动构建并部署。
如果几分钟后 GitHub 页面仍未显示，前往的仓库设置`Deploy from a branch`，并确保 GitHub 页面的发布源分支设置为`gh-pages`

### 公告栏

创建`overrides/main.html`:

```html
{% extends "base.html" %} {% block announce %}
<strong>📢 公告栏。Announcement Bar. </strong>
{% endblock %}
```

在`mkdocs.yml`中添加:

```yml
theme:
  custom_dir: overrides
  features:
    - announce.dismiss # 允许用户关闭顶部公告栏
```

### 网页版本控制

```bash
uv add --dev mike
```

在`mkdocs.yml`中添加:

```yml
extra:
  version:
    provider: mike
    alias: true
```

在`overrides/main.html`添加 (只有拥有该内容的版本才会显示过时信息。如果以前的网页没有该内容，就不会显示):

```html
{% extends "base.html" %} {% block outdated %} 你正在查看的不是最新版本。
<a href="{{ '../' ~ base_url }}">
  <strong>点此查看最新版。</strong>
</a>
{% endblock %}
```

> 如果`{% extends "base.html" %}`已经写过了，就不用再写了。

修改`.github/workflows/website_deploy.yml`:

```yml
name: Website Deploy
on:
  release:
    types: [published] # 必须改成 release 时触发
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Configure Git Credentials
        run: |
          git config user.name github-actions[bot]
          git config user.email 41898282+github-actions[bot]@users.noreply.github.com
      - uses: actions/setup-python@v5
        with:
          python-version: 3.x
      - run: echo "cache_id=$(date --utc '+%V')" >> $GITHUB_ENV
      - uses: actions/cache@v4
        with:
          key: mkdocs-material-${{ env.cache_id }}
          path: ~/.cache
          restore-keys: |
            mkdocs-material-
      - run: |
          pip install uv
          uv sync
          git fetch
          # ${{ github.event.release.tag_name }}依赖release作为触发源
          uv run mike deploy --push --update-aliases ${{ github.event.release.tag_name }} latest
          uv run mike set-default --push latest
```
