# 贡献 Supervision 🛠️

感谢您对贡献 Supervision 感兴趣！

我们正在积极改进这个库，以减少您在解决常见计算机视觉问题时所需的工作量。

## 行为准则

请阅读并遵守我们的 [行为准则](https://supervision.roboflow.com/latest/code_of_conduct/)。本文档概述了我们项目所有参与者应遵循的行为。

## 目录

- [贡献指南](#contribution-guidelines)
    - [贡献新功能](#contributing-features)
- [如何贡献更改](#how-to-contribute-changes)
- [贡献者安装指南](#installation-for-contributors)
- [代码风格与质量](#code-style-and-quality)
    - [Pre-commit 工具](#pre-commit-tool)
    - [Docstrings](#docstrings)
    - [类型检查](#type-checking)
- [文档](#documentation)
- [Cookbooks](#cookbooks)
- [测试](#tests)
- [许可证](#license)

## 贡献指南

我们欢迎以下方面的贡献：

1. 为库添加新功能（下方提供指南）。
2. 改进我们的文档并添加示例，以清晰地说明如何利用 supervision 库。
3. 报告项目中的 bug 和问题。
4. 提交新功能请求。
5. 提高我们的测试覆盖率。

### 贡献新功能 ✨

Supervision 旨在提供通用的实用工具来解决问题。因此，我们专注于可以对广泛项目产生影响的贡献。

例如，在图像中的任何位置计算穿过一条线的物体数量是计算机视觉中的一个常见问题，但计算穿过距离图像 75% 处的线的物体数量则不太有用。

在贡献新功能之前，请考虑提交一个 Issue 来讨论该功能，以便社区能够发表意见并提供帮助。

## 如何贡献更改

首先，将此存储库 fork 到您自己的 GitHub 账户。点击 `supervision` 存储库右上角的“fork”按钮即可开始：

![Forking the repository](https://media.roboflow.com/fork.png)

![Creating a repository fork](https://media.roboflow.com/create_fork.png)

然后，使用 `git clone` 将项目代码下载到您的计算机。

您还应该将 `roboflow/supervision` 设置为“upstream”远程（即告诉 git Supervision 的参考存储库是您 fork 的来源）：

```bash
git remote add upstream https://github.com/roboflow/supervision.git
git fetch upstream
```

使用 `git checkout` 命令切换到新分支：

```bash
git checkout -b <scope>/<your_branch_name> upstream/develop
```

您为分支选择的名称应描述您要进行的更改，并以适当的前缀开头：

- `feat/`: 用于新功能（例如 `feat/line-counter`）
- `fix/`: 用于 bug 修复（例如 `fix/memory-leak`）
- `docs/`: 用于文档更改（例如 `docs/update-readme`）
- `chore/`: 用于例行任务、维护或工具更改（例如 `chore/update-dependencies`）
- `test/`: 用于添加或修改测试（例如 `test/add-unit-tests`）
- `refactor/`: 用于代码重构（例如 `refactor/simplify-algorithm`）

对项目代码进行您想要的任何更改，然后运行以下命令来提交您的更改：

```bash
git add -A
git commit -m "feat: add line counter functionality"
git push -u origin <your_branch_name>
```

使用约定俗成的提交消息来清晰描述您的更改。格式如下：

<type>\[可选范围\]: <description>

常见的类型包括：

- feat: 一个新功能
- fix: 一个 bug 修复
- docs: 仅文档更改
- style: 不影响代码含义的更改 (空格，格式等)
- refactor: 既不修复 bug 也不添加功能的代码更改
- perf: 改进性能的代码更改
- test: 添加缺失的测试或更正现有的测试
- chore: 对构建过程或辅助工具和库进行的更改

然后，返回到 `supervision` 存储库的 fork，点击“Pull Requests”，然后点击“New Pull Request”。

![Opening a pull request](https://media.roboflow.com/open_pr.png)

在提交 PR 之前，请确保 `base` 分支是 `develop`。

在下一页，审阅您的更改，然后点击“Create pull request”：

![Configuring a pull request](https://media.roboflow.com/create_pr_submit.png)

接下来，为您的 Pull Request 撰写描述，然后再次点击“Create pull request”以提交审核：

![Submitting a pull request](https://media.roboflow.com/write_pr.png)

在创建新函数时，请确保您具备以下条件：

1. 为函数和所有参数提供 docstrings。
2. 为函数编写单元测试。
3. 在文档中提供函数示例。
4. 在我们的文档中创建条目以自动生成函数的文档。
5. 尽可能提供一个 Google Colab，其中包含最少的代码来测试新功能或重现 PR。请确保 Google Colab 可以毫无问题地访问。

当您提交 Pull Request 时，`cla-assistant` GitHub bot 会要求您签署贡献者许可协议 (CLA)。我们只能响应已签署项目 CLA 的贡献者的 PR。

所有 Pull Requests 都将由项目维护者进行审查。我们将提供反馈并在必要时要求进行更改。

PR 必须通过所有测试和 linting 要求，然后才能合并。

## 贡献者安装指南

在开始项目工作之前，请设置您的开发环境：

1. 克隆您 fork 的项目（建议浅克隆 develop 分支）：

    **选项 A：大多数贡献者推荐（浅克隆 develop 分支）：**

    ```bash
    git clone --depth 1 -b develop https://github.com/YOUR_USERNAME/supervision.git
    cd supervision
    ```

    将 `YOUR_USERNAME` 替换为您的 GitHub 用户名。

    > 注意：使用 `--depth 1` 创建一个具有最少历史记录的浅克隆，`-b develop` 确保您从 development 分支开始。这大大减少了下载量，同时提供了贡献所需的一切。

    **选项 B：完整存储库克隆（如果您需要完整的历史记录）：**

    ```bash
    git clone https://github.com/YOUR_USERNAME/supervision.git
    cd supervision
    ```

2. 创建并激活虚拟环境：

    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    ```

3. 安装 `uv`：

    请按照 [uv 安装页面](https://docs.astral.sh/uv/getting-started/installation/) 上的说明进行操作。

4. 安装项目依赖项：

    ```bash
    uv pip install -r pyproject.toml --extra dev --extra docs --extra metrics
    ```

5. 运行 pytest 来验证设置：

    ```bash
    uv run pytest
    ```

## 🎨 代码风格与质量

### Pre-commit 工具

本项目使用 [pre-commit](https://pre-commit.com/) 工具来维护代码质量和一致性。在提交 Pull Request 或进行任何提交之前，运行 pre-commit 工具以确保您的更改符合项目指南非常重要。

此外，我们将 pre-commit GitHub Action 集成到了我们的工作流程中。这意味着每次打开 Pull Request 时，都会自动强制执行 pre-commit 检查，从而简化代码审查流程并确保所有贡献都符合我们的质量标准。

要运行 pre-commit 工具，请按照以下步骤操作：

1. 运行以下命令安装 pre-commit：`uv pip install -r pyproject.toml --extra dev`。这将不仅安装 pre-commit，还将安装项目的所有依赖项和开发依赖项。

2. 安装 pre-commit 后，导航到项目的根目录。

3. 运行命令 `pre-commit run --all-files`。这将针对修改过的文件执行项目中配置的 pre-commit 钩子。如果发现任何问题，pre-commit 工具将提供有关如何解决这些问题的反馈。进行必要的更改并重新运行 pre-commit 命令，直到所有问题都得到解决。

4. 您也可以通过执行 `pre-commit install` 来将 pre-commit 安装为 git 钩子。每次执行 `git commit` 时，pre-commit 都会自动为您运行。

### Docstrings

`supervision` 中的所有新函数和类都应包含 docstrings。这是添加到库中的任何新函数和类的先决条件。

`supervision` 遵循 [Google Python docstring 风格](https://google.github.io/styleguide/pyguide.html#383-functions-and-methods)。在为您的贡献编写 docstrings 时，请参考此风格指南。

### 类型检查

到目前为止，**还没有使用 mypy 进行类型检查**。请参阅 [issue](https://github.com/roboflow-ai/template-python/issues/4)。

## 📝 文档

`supervision` 的文档存储在名为 `docs` 的文件夹中。项目文档使用 `mkdocs` 构建。

要运行文档，请使用 `uv pip install -r pyproject.toml --extra dev --extra docs` 命令安装项目需求。然后，运行 `mkdocs serve` 来启动文档服务器。

您可以在 [mkdocs 网站](https://www.mkdocs.org/) 上了解更多关于 mkdocs 的信息。

## 🧑‍🍳 Cookbooks

我们一直在寻找新的示例和 cookbooks 来添加到 `supervision` 的文档中。如果您有一个对他人有帮助的用例，请提交一个 PR 来分享您的示例。以下是提交新示例的指南：

- 在 [`docs/notebooks`](https://github.com/roboflow/supervision/tree/develop/docs/notebooks) 文件夹中创建一个新的 notebook。
- 在 [`docs/theme/cookbooks.html`](https://github.com/roboflow/supervision/blob/develop/docs/theme/cookbooks.html) 中添加指向新 notebook 的链接。确保添加新 notebook 的路径、标题、标签、作者和 supervision 版本。
- 使用 [Count Objects Crossing the Line](https://supervision.roboflow.com/develop/notebooks/count-objects-crossing-the-line/) 示例作为新示例的模板。
- 冻结您使用的 `supervision` 版本。
- 在 notebook 的顶部放置一个适当的 Open in Colab 按钮。您可以在前面提到的 `Count Objects Crossing the Line` cookbook 中找到此类按钮的示例。
- Notebook 应该是自包含的。如果您依赖于外部数据（视频、图像等）或库，请在 notebook 中包含下载和安装命令。
- 使用适当的注释来注解代码，包括指向描述您使用的每个工具的文档的链接。

## 🧪 测试

我们使用 [`pytests`](https://docs.pytest.org/en/7.1.x/) 来运行我们的测试。

## 📄 许可证

通过贡献，您同意您的贡献将根据 [MIT 许可证](https://github.com/roboflow/supervision/blob/develop/LICENSE.md) 进行许可。