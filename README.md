# Reusable Workflow

简体中文 | [English](./README.en.md)

`reusable-workflow` 是一组可重用的`GitHub Workflow`。你可以引用这个项目的`GitHub Workflow`，并在自己的项目中使用它，而无需重新编辑。

## Push Mirror Git Repository

这是一个用于完全镜像单个`Git Repository`，并将这个镜像批量推送到其他`Git Repository`平台的`GitHub Workflow`。

### 如何使用

```yml
# 自定义的 Github Workflow 名
name: My push mirror Git Repository

# 自定义 Github Workflow 的触发条件
on: [ push, delete, create ]

jobs:
  # 自定义的 job 名
  call-push-mirror-git-repository:

    # --------------------------------------在此以上的代码可以根据需求任意更改--------------------------------------
    # -----------------------------在此之后的代码为：调用该 GitHub Workflow 的核心步骤（不能更改）-----------------------------

    uses: HeavenZhi/reusable-workflow/.github/workflows/push-mirror-git-repository.yml@main
    with:
      source_repo: ${{ vars.SOURCE_REPO }}
      destination_repo: ${{ vars.DESTINATION_REPO }}
    secrets:
      ssh_private_key: ${{ secrets.SSH_PRIVATE_KEY }}
```

使用方法粗略总括为三步：

1. 在您的`GitHub's Git Repository`的`Setting`中的`Secrets and variables`里新增三项：
   1. 在`Repository secrets`中新增：`SSH_PRIVATE_KEY`，其值为`SSH`的私钥，它需要具有对`Git Repository`进行`pull`和`push`操作的权限
   2. 在`Repository variables`中新增：`SOURCE_REPO`，其值为源仓库地址(最好使用`SSH`的`Git`仓库地址)
   3. 在`Repository variables`中新增：`DESTINATION_REPO`，其值为目标仓库地址(最好使用`SSH`的`Git`仓库地址)，多个地址可以使用`|`分隔
2. 在您需要进行镜像同步的`GitHub's Git Repository`的根目录中新增目录`.github/workflows`，并在目录中创建文件后缀名为`.yml`或`.yaml`的文件
3. 在上一步创建的文件中调用本`GitHub Workflow`并传入对应参数