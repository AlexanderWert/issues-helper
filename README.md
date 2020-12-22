# 🤖 Issue Helper

English | [简体中文](./README.zh-CN.md)

一个帮你处理 issue 的 GitHub Action

## 😎 为什么用 GitHub Action？

1. 完全免费。
2. 全自动操作。
3. 托管于 GitHub 服务器，只要 GitHub 不宕机，它就会一直跑下去。

> Private 项目每月有 2000 次的限制，[具体查看](https://github.com/settings/billing)。Public 项目无限制。

## 列 表

- ⭐ 基 础
  - [`add-assignees`](#add-assignees)
  - [`add-labels`](#add-labels)
  - [`close-issue`](#close-issue)
  - [`create-comment`](#create-comment)
  - [`create-issue`](#create-issue)
  - [`delete-comment`](#delete-comment)
  - [`lock-issue`](#lock-issue)
  - [`open-issue`](#open-issue)
  - [`remove-assignees`](#remove-assignees)
  - [`set-labels`](#set-labels)
  - [`unlock-issue`](#unlock-issue)
  - [`update-comment`](#update-comment)
  - [`update-issue`](#update-issue)
- ⭐ 进 阶
  - [`find-comment`](#find-comments)
- ⭐ 高 级
  - 222
- 🌰 例 子
  - [`find-comments + create-comment + update-comment`](#find-comments--create-comment--update-comment)

## 🚀 使 用

### ⭐ 基 础

为了更好的展示功能，下面以实际场景举例，请灵活参考。

#### `add-assignees`

当一个 issue 新增或修改时，将这个 issue 指定某人或多人。

```yml
name: Add Assigness

on:
  issues:
    types: [opened, edited]

jobs:
  add-assigness:
    runs-on: ubuntu-latest
    steps:
      - name: Add assigness
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'add-assignees' or ['add-assignees']
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
          assignees: 'xxx' or ['xxx'] or ['xx1', 'xx2']
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| assignees | 指定人。当不填或者为空字符、空数组时，不指定 | string \| string\[] | ✖ | v1 |

- 其中的 `name` 可根据自行根据实际情况修改
- [on 参考](#github-docs)
- `${{ github.event.issue.number }}` 表示当前 issue，[更多参考](https://docs.github.com/en/free-pro-team@latest/developers/webhooks-and-events)。

⏫ [返回列表](#列-表)

#### `add-labels`

当一个新增的 issue 内容不包含指定格式时，为这个 issue 添加 labels。

```yml
name: Add Labels

on:
  issues:
    types: [opened]

jobs:
  add-labels:
    runs-on: ubuntu-latest
    if: github.event.issue.body.indexOf('Create by specifying way') == -1
    steps:
      - name: Add labels
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'add-labels'
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
          labels: 'bug' or ['bug'] or ['bug1', 'bug2']
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| labels | 新增的 labels。当不填或者为空字符、空数组时，不新增 | string \| string\[] | ✖ | v1 |

⏫ [返回列表](#列-表)

#### `close-issue`

关闭指定 issue。当输入 `body` 时，会同时进行评论。

```yml
- name: Close issue
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'close-issue'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: xxx
      body: 'This is auto closed.'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| body | 关闭 issue 时，可进行评论 | string | ✖ | v1 |

⏫ [返回列表](#列-表)

#### `create-comment`

当新增一个指定 label 时，对该 issue 进行评论。

```yml
name: Create Comment

on:
  issues:
    types: [labeled]

jobs:
  create-comment:
    runs-on: ubuntu-latest
    if: github.event.label.name == 'xxx'
    steps:
      - name: Create comment
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'create-comment'
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
          body: |
            Hello ${{ github.event.issue.user.login }}. Add some comments.

            你好 ${{ github.event.issue.user.login }}。巴拉巴拉。
          contents: '+1' or ['+1', 'heart']
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| body | 新增评论的内容  | string | ✖ | v1 |
| contents | 为新增评论的增加 [reaction](#reactions-types) | string \| string\[] | ✖ | v1 |

- `body` 默认为：`Currently at ${owner}/${repo}. And this is default comment.`
  - 其中 `${owner}/${repo}` 表示当前仓库
- 返回 `comment-id`，可用于之后操作。[用法参考](#输出使用)
- `${{ github.event.issue.user.login }}` 表示该 issue 的创建者

⏫ [返回列表](#列-表)

#### `create-issue`

感觉新增 issue 使用场景不多。这里举例，每月 1 号 UTC 00:00 新增一个 issue。

```yml
name: Create Issue

on:
  schedule:
    - cron: "0 0 1 * *"

jobs:
  create-issue:
    runs-on: ubuntu-latest
    steps:
      - name: Create issue
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'create-issue'
          token: ${{ secrets.GITHUB_TOKEN }}
          title: 'xxxx'
          body: 'xxxx'
          labels: 'xx'
          assignees: 'xxx'
          contents: '+1'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| title | 新增 issue 的标题 | string | ✖ | v1 |
| body | 新增 issue 的内容 | string | ✖ | v1 |
| labels | 为新增 issue 添加 labels | string \| string\[] | ✖ | v1 |
| assignees | 为新增 issue 添加 assignees | string \| string\[] | ✖ | v1 |
| contents | 为新增 issue 增加 [reaction](#reactions-types) | string \| string\[] | ✖ | v1 |

- `title` 默认为：`Default Title`
- `body` 默认值同上
- 返回 `issue-number`，[用法参考](#输出使用)

⏫ [返回列表](#列-表)

#### `delete-comment`

根据 [`comment_id`](#comment_id-获取) 删除指定评论。

```yml
- name: Delete comment
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'delete-comment'
      token: ${{ secrets.GITHUB_TOKEN }}
      comment-id: xxx
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| comment-id | 指定的 comment | number | ✔ | v1 |

⏫ [返回列表](#列-表)

#### `lock-issue`

当新增 `invalid` label 时，对该 issue 进行锁定。

```yml
name: Lock Issue

on:
  issues:
    types: [labeled]

jobs:
  lock-issue:
    runs-on: ubuntu-latest
    if: github.event.label.name == 'invalid'
    steps:
      - name: Lock issue
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'lock-issue'
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |

⏫ [返回列表](#列-表)

#### `open-issue`

打开指定 issue。

```yml
- name:  Open issue
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'open-issue'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: xxx
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| body | 打开 issue 时，可进行评论 | string | ✖ | v1 |

⏫ [返回列表](#列-表)

#### `remove-assignees`

移除 issue 指定人员。

```yml
- name: Remove assignees
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'remove-assignees'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: ${{ github.event.issue.number }}
      assignees: 'xx'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| assignees | 移除的指定人。当不填或者为空字符、空数组时，不进行移除 | string \| string\[] | ✖ | v1 |

⏫ [返回列表](#列-表)

#### `set-labels`

设置 issue 的 labels。

```yml
- name: Set labels
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'set-labels'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: ${{ github.event.issue.number }}
      labels: 'xx'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| labels | labels 设置。当不填或者为空字符、空数组时，会移除所有 | string \| string\[] | ✖ | v1 |

⏫ [返回列表](#列-表)

#### `unlock-issue`

解锁指定 issue。

```yml
- name: Unlock issue
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'unlock-issue'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: ${{ github.event.issue.number }}
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |

⏫ [返回列表](#列-表)

#### `update-comment`

根据 [`comment_id`](#comment_id-获取) 更新指定评论。

下面的例子展示的是，为每个新增的 comment 增加 👀 。

```yml
name: Add eyes to each comment

on:
  issue_comment:
    types: [created]

jobs:
  update-comment:
    runs-on: ubuntu-latest
    steps:
      - name: Update comment
          uses: actions-cool/issue-helper@v1
          with:
            actions: 'update-comment'
            token: ${{ secrets.GITHUB_TOKEN }}
            comment-id: ${{ github.event.comment.id }}
            contents: 'eyes'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| comment-id | 指定的 comment | number | ✔ | v1 |
| body | 更新 comment 的内容 | string | ✖ | v1 |
| update-mode | 更新模式。`replace` 替换，`append` 附加 | string | ✖ | v1 |
| contents | 为 comment 增加 [reaction](#reactions-types) | string \| string\[] | ✖ | v1 |

- `body` 不输入时，会保持原有
- `update-mode` 为 `append` 时，会进行附加操作。非 `append` 都会进行替换。仅对 `body` 生效。

⏫ [返回列表](#列-表)

#### `update-issue`

根据 `issue-number` 更新指定 issue。

```yml
- name: Update issue
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'update-issue'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: ${{ github.event.issue.number }}
      state: 'open'
      title: 'xxx'
      body: 'xxxx'
      update-mode: 'replace'
      labels: 'xx'
      assignees: 'xxx'
      contents: '+1'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| state | 修改 issue 的状态，可选值 `open` `closed` | string | ✖ | v1 |
| title | 修改 issue 的标题 | string | ✖ | v1 |
| body | 修改 issue 的内容 | string | ✖ | v1 |
| labels | 修改 issue 的 labels | string \| string\[] | ✖ | v1 |
| assignees | 修改 issue 的 assignees | string \| string\[] | ✖ | v1 |
| contents | 为修改的 issue 增加 [reaction](#reactions-types) | string \| string\[] | ✖ | v1 |

- `state` 默认为 `open`
- 当可选项不填时，会保持原有

⏫ [返回列表](#列-表)

### ⭐ 进 阶

#### `find-comments`

查找当前仓库 1 号 issue 中，创建者是 k ，内容包含 `this` 的评论列表。

```yml
- name: Find comments
    uses: actions-cool/issue-helper@v1
    with:
      actions: 'find-comments'
      token: ${{ secrets.GITHUB_TOKEN }}
      issue-number: 1
      comment-auth: k
      body-includes: 'this'
```

| 参数 | 描述 | 类型 | 必填 | 版本 |
| -- | -- | -- | -- | -- |
| actions | actions 类型，当为数组时，会进行多个操作 | string \| string\[] | ✔ | v1 |
| token | [token 说明](#token) | string | ✖ | v1 |
| issue-number | 指定的 issue | number | ✔ | v1 |
| comment-auth | 评论创建者，不填时会查询所有 | string | ✖ | v1 |
| body-includes | 评论内容包含过滤，不填时无校验 | string | ✖ | v1 |

- 返回 `comments`, 格式如下：

```js
[
  {id: 1, body: 'xxx'},
  {id: 2, body: 'xxxx'}
]
```

⏫ [返回列表](#列-表)

### ⭐ 高 级

## 🌰 例 子

以下列举一些例子，请灵活参考。

### `find-comments + create-comment + update-comment`

假设场景：当 issue 修改时，查找是否有 k 创建的包含 `error` 的评论，如果只有一个，则更新该 comment，如果没有，则新增一个 comment。

```yml
name: Test

on:
  isssue:
    types: [edited]

jobs:
  do-test:
    runs-on: ubuntu-latest
    steps:
      - name: find comments
        uses: actions-cool/issue-helper@v1
        id: fcid
        with:
          actions: 'find-comments'
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
          comment-auth: k
          body-includes: 'error'

      - name: create comment
        if: ${{ steps.fcid.outputs.comments.length == 0 }}
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'create-comment'
          token: ${{ secrets.GITHUB_TOKEN }}
          issue-number: ${{ github.event.issue.number }}
          body: 'Some error!'

      - name: update comment
        if: ${{ steps.fcid.outputs.comments.length == 1 }}
        uses: actions-cool/issue-helper@v1
        with:
          actions: 'update-comment'
          token: ${{ secrets.GITHUB_TOKEN }}
          comment-id: ${{ steps.fcid.outputs.comments[0].id }}
          body: 'Some error again!'
          update-mode: 'append'
```

⏫ [返回列表](#列-表)

## 🎁 参 考

### token

需拥有 push 权限的人员 token。

- [个人 token 申请](https://github.com/settings/tokens)
  - 需勾选 `Full control of private repositories`
- 项目添加 secrets
  - 选择 settings，选择 secrets，选择 `New repository secret`
  - `Name` 与 actions 中保持一致
  - `Value` 填写刚才个人申请的 token

当 actions 不填写 token 时，会默认为 github-actions <kbd>bot</kbd>。适度使用。

⏫ [返回列表](#列-表)

### 输出使用
```yml
- name: Create issue
  uses: actions-cool/issue-helper@v1
  id: createissue
  with:
    actions: 'create-issue'
    token: ${{ secrets.GITHUB_TOKEN }}
- name: Check outputs
  run: echo "Outputs issue_number is ${{ steps.createissue.outputs.issue-number }}"
```
### GitHub Docs

- [GitHub Actions 语法](https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions#on)
- [工作流触发机制](https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows)

⏫ [返回列表](#列-表)

### Reactions types

| content | emoji |
| -- | -- |
| `+1` | 👍 |
| `-1` | 👎 |
| `laugh` | 😄 |
| `confused` | 😕 |
| `heart` | ❤️ |
| `hooray` | 🎉 |
| `rocket` | 🚀 |
| `eyes` | 👀 |

⏫ [返回列表](#列-表)

### `comment_id` 获取

点击某个评论右上角 `···` 图标，选择 `Copy link`，url 末尾数字即是 `comment_id`。

⏫ [返回列表](#列-表)

## 谁在使用？

## LICENSE

[MIT](https://github.com/actions-cool/issue-helper/blob/main/LICENSE)
