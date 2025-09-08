# HELLO_WORLD

这是一个用来学习、测试各种功能的仓库，顺带记录一些学习笔记。

## release-please

release-please是一个可以自动化的版本管理工具，可以帮助你管理版本，并且在每次发布时自动化的生成你的CHANGELOG。

初创仓库时，需要：

- `release-please-config.json`: 用来配置release-please
- `.release-please-manifest.json`: 用来记录当前版本，初创为“{}”，否则,例如“{".":"1.6.1"}”
- `.github/workflows/release.yml`: 用来触发github actions
- 每个仓库需要配置 RELEASE_PLEASE_ACCESS_TOKEN 密钥
