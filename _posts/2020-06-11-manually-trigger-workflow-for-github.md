---
layout: post
title: "手动触发 GitHub 工作流"
category: Linux
tags: [GitHub, CLI]
---

目前，GitHub 的网页并没有提供手动触发工作流，这在很多时候非常受限，如果在工作流文件中，`on.push` 设置非常宽松，一个简单的 readme 修改，一个 fork，等等，也会触发工作流。如果设置严格只跟踪特定的几个文件，在我们 commit 了除关键文件外的修改时，却不会触发工作流。

我想要得到什么样的目的呢，我希望，可以手动触发工作流，通过一些关键命令，当然，我也习惯用 curl 来发送 POST。

实际上，GitHub 确实提供了这样的功能，即 `repository_dispatch` 事件，通过如下的 GitHub api 来交互，以及 `workflow_dispatch` 事件。

<!-- more -->

#### repository_dispatch

    POST /repos/:owner/:repo/dispatches

让我们修改工作流文件，加入如下修改

```yaml
name: any-name
on:
  repository_dispatch:
    types: rebuild
```

如此，GitHub 上的项目，会接受来自你发送的 `'event_type': 'rebuild'` 的命令，来触发你定义的工作流。

当然在此之前，你需要首先创建你的一个私人令牌，专用于在手动触发时的认证，注意不要开启过多权限。

右上角个人下拉菜单 -- Settings -- Developer settings -- Personal acces tokens

注意，不要给予过多的权限，我个人只勾选了 public_repo


- [ ]  repo
    - [ ]  repo:status
    - [ ]  repo_deployment
    - [x]  public_repo
    - [ ]  repo:invite
    - [ ]  security_events

然后你可以在你项目没有变动的情况下，手动触发工作流。

```bash
curl -H "Accept: application/Accept: application/vnd.github.v3.full+json" \
-H "Authorization: token a1a2a3a4a5a6a7a8a9b1b2b3b4b5b6b7b8b9c1c2" \
--request POST \
--data '{"event_type": "rebuild"}' \
https://api.github.com/repos/yourname/yourrepo/dispatches
```

一些场景下，我想传入一些参数呢？譬如版本号变量？当然可以：

```bash
curl -H "Accept: application/Accept: application/vnd.github.v3.full+json" \
-H "Authorization: token a1a2a3a4a5a6a7a8a9b1b2b3b4b5b6b7b8b9c1c2" \
--request POST \
--data '{"event_type": "rebuild", "client_payload": { "version": "0.0.2"}}' \
https://api.github.com/repos/yourname/yourrepo/dispatches
```

再修改下工作流文件

```yaml
name: any-name
on:
  repository_dispatch:
    types: rebuild

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Prepare
        id: prepare
        run: |
          VERSION=${% raw %}{{ github.event.client_payload.version }}{% endraw %}
          [[ ! -n $VERSION ]] && VERSION=0.0.1
```

#### workflow_dispatch

`workflow_dispatch` 就比较简单了，你可以直接在 actions 网页手动去提交，当然你也可以通过 `github.event.inputs` 传入参数。如：

```yaml
name: any-name

on:
  workflow_dispatch:
    inputs:
      version:
        description: 'version'
        required: true
        default: '1.0.0'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Prepare
        id: prepare
        run: |
          VERSION=${% raw %}{{ github.event.inputs.version }}{% endraw %}
          echo ::set-output name=build_args::VERSION=${VERSION}
```

效果如下图：

![workflow-dispatch](/cdn/images/2020/06/workflow-dispatch.png)

如此当你想触发工作流的时候，你不用特意去 commit 任何文件了。

参考资料：

- <https://docs.github.com/actions/reference/events-that-trigger-workflows>
- <https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line>
- <https://developer.github.com/v3/repos/#create-a-repository-dispatch-event>
- <https://developer.github.com/v3/media/#request-specific-version>
