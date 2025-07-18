# 发布流程

本文档概述了 supervision 如何发布到生产环境。

假定您已经完成了代码的更改，并且准备好了发布说明。

1. 确保所有必需的更改都已合并到 `develop` 分支。
2. 创建并合并一个 PR，将 `develop` 分支合并到 `main` 分支，该 PR 应包含：
    - 更新 `pyproject.toml` 中项目版本的提交。
    - 发布期间进行的所有更改。
3. 为包含新 supervision 版本的提交打上标签。
    - 确保您已从 `main` 分支拉取！
    - 验证最新的合并提交是否存在。`git log`。
    - 运行 `git tag x.y.z`，其中 `x.y.z` 是您的版本号。
    - 使用 `git log` 进行检查。
    - 运行 `git push origin --tags`。
    - 提交标签后，[PyPi](https://pypi.org/project/supervision/) 应该会更新到新版本。请检查此项！
4. 创建并合并一个 PR，将 `main` 分支合并到 `develop` 分支。
5. 运行 GitHub 上的 [Supervision 发布文档工作流 📚](https://github.com/roboflow/supervision/actions/workflows/publish-release-docs.yml) 来更新文档。
    - 从下拉菜单中选择 `main` 分支。
6. 在 GitHub 上创建一个发布。
    - 前往 releases
    - 将发布说明分配给第 3 步中创建的标签。
    - 发布此版本。