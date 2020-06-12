---
layout: post
title: "Rime 输入法配置小结"
category: Mac
tags: [Rime]
---

#### 一、 概述

输入法是用户的基础工具，每天都用得到，但是现在的各类云输入法在方便用户的同时，敏感数据非加密传输等隐私问题就一直没有中断过，方便用户输入就是当今各商业输入法的一块遮羞布。最近一直看到圈里流传 Rime 输入法，就花了点时间折腾了一下。

![Rime](/cdn/images/2015/05/rime.png)

RIME / 中州韵输入法引擎，是一个跨平台的输入法算法框架。

基于这一框架，Rime 开发者与其他开源社区的参与者在 Windows、Mac OS X、Linux、Android 平台上创造了不同的输入法前端实现。

<!-- more -->

- Windows
    - Weasel 小狼毫
- Mac OS X
    - Squirrel 鼠须管
- Linux
    - ibus-rime 基于 IBus 输入法框架
    - fcitx-rime 基于 Fcitx 输入法框架
- Android
    - Tongwen Rime 同文

OS X 下的用户配置文件在 **~/Library/Rime** 目录下，下面记录下使用 Rime 的一些个人配置。

##### 1. installation.yaml

```yaml
distribution_code_name: Squirrel
distribution_name: "鼠鬚管"
distribution_version: 0.9.26.1
install_time: "Mon May 27 10:56:16 2015"
installation_id: "Rime"
rime_version: 1.2.9
```

##### 2. default.custom.yaml

```yaml
patch:
  switcher:
    abbreviate_options: true
    caption: 〔切换〕
    fold_options: true
    hotkeys:
      - "Control+grave"          # control + `
    save_options:
      - full_shape
      - ascii_punct
      - simplification
      - extended_charset
  menu:
    page_size: 7                 # 候选词数量
    #style/horizontal: true       # 候选窗水平显示

  schema_list:
    - schema: luna_pinyin_simp

  ascii_composer/switch_key/Shift_L: clear
  ascii_composer/switch_key/Shift_R: clear
```

##### 3. squirrel.custom.yaml

```yaml
patch:
  show_notifications_via_notification_center: true
  us_keyboard_layout: true                              # 美式键盘布局
  show_notifications_when: appropriate                  # 状态通知，适当，也可设为全开（always）全关（never）


  style:
    color_scheme: light                                 # 配色方案名称

  preset_color_schemes:
    light:
      name: user                                        # 作者名
      author: "user <user@domain.com>"                  # 作者

      horizontal: true                                  # 候选条横向显示
      inline_preedit: true                              # 启用内嵌编码模式，候选条首行不显示拼音
      candidate_format: "%c\u2005%@\u2005"              # 用 1/6 em 空格 U+2005 来控制编号 %c 和候选词 %@ 前后的空间。

      corner_radius: 5                                  # 候选条圆角半径
      border_height: 7                                  # 窗口边界高度，大于圆角半径才生效
      border_width: 7                                   # 窗口边界宽度，大于圆角半径才生效
      back_color: 0xFFFFFF                              # 候选条背景色
      border_color: 0xE0B693                            # 边框色
      font_face: "PingFangSC-Regular"                   # 候选词字体
      font_point: 18                                    # 预选栏文字字号
      label_font_face: "PingFangSC-Light"               # 候选词编号字体
      label_font_point: 14                              # 预选栏编号字号
      candidate_text_color: 0x000000                    # 预选项文字颜色
      text_color: 0x000000                              # 拼音行文字颜色，24位色值，16进制，BGR顺序
      comment_text_color: 0x999999                      # 拼音等提示文字颜色
      hilited_text_color: 0xFF6941                      # 高亮拼音 (需要开启内嵌编码)
      hilited_candidate_text_color: 0xFF6941            # 第一候选项文字颜色
      hilited_candidate_back_color: 0xFFFFFF            # 第一候选项背景背景色
      hilited_comment_text_color: 0xFF6941              # 注解文字高亮

  app_options:
    com.blacktree.Quicksilver: &a
      ascii_mode: false
    com.googlecode.iterm2: *a
    com.alfredapp.Alfred: *a
    com.runningwithcrayons.Alfred-2: *a
    org.vim.MacVim: *a
    com.apple.Terminal: *a
```

##### 4. luna_pinyin_simp.custom.yaml

```yaml
patch:
  switches:
    - name: ascii_mode
      reset: 1                                          # 默认英文
      states: ["中", "英"]
    - name: full_shape
      reset: 0                                          # 默认半角
      states: ["半", "全"]
    - name: zh_simp
      reset: 1                                          # 预设简体
      states: ["繁", "简"]
    - name: ascii_punct
      states: ["。，", "．，"]
    - options: [utf8, gbk]                              # 字符集
      reset: 1                                          # 默认 GBK
      states: ["utf8", "gbk"]

  "engine/filters/@next": charset_filter@gbk            # 默认 GBK
  "engine/translators/@next": reverse_lookup_translator

  translator:
    dictionary: luna_pinyin.extended
    prism: luna_pinyin_simp

  "speller/algebra/@before 0": xform/^([b-df-hj-np-tv-z])$/$1_/

  punctuator:                                           # 符号快速输入和部分符号的快速上屏
    import_preset: symbols
    full_shape:
      "\\": "、"
    half_shape:
      "#": "#"
      "`": "`"
      "~": "~"
      "@": "@"
      "=": "="
      "/": ["/", "÷"]
      '\': "、"
      "'": {pair: ["「", "」"]}
      "[": ["【", "["]
      "]": ["】", "]"]
      "$": ["¥", "$", "€", "£", "¢", "¤"]
      "<": ["《", "〈", "«", "<"]
      ">": ["》", "〉", "»", ">"]

  recognizer:
    patterns:
      email: "^[A-Za-z][-_.0-9A-Za-z]*@.*$"
      uppercase: "[A-Z][-_+.'0-9A-Za-z]*$"
      url: "^(www[.]|https?:|ftp[.:]|mailto:|file:).*$|^[a-z]+[.].+$"
      punct: "^/([a-z]+|[0-9]0?)$"
      reverse_lookup: "`[a-z]*'?$"
```

##### 5. luna_pinyin.extended.dict.yaml

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin
    ...

**import_tables_** 是添加扩展词库用的，可以添加第三方的词库文件，譬如 **luna_pinyin.name.dict.yaml**，则添加格式如下

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin
      - luna_pinyin.hanyu
      - luna_pinyin.poetry
    ...

##### 6. 特别说明 ibus-rime

后选框样式需要文件 ibus_rime.custom.yaml

    patch:
      style/horizontal: true

###### 7. 基本概念

建议配置文件采用对默认配置 patch 的方式，注意以下亮点

- 如果对原配置 name.yaml 进行自定义配置，则创建文件 name.custom.yml，再在新文件中修改
- 文件中第一行，以 `patch:` 开头

譬如修改 default.yaml，则创建 default.custom.yaml
如果要修改左右 shift 键行为，则在 default.custom.yaml 添加

    patch:
      ascii_composer/switch_key/Shift_L: clear
      ascii_composer/switch_key/Shift_R: clear

这里用到了 `\` 将 key 折叠起来，折叠与不折叠的区别在于， `key1/key2/key3` 只 patch key3，否则就直接 patch key1。所以是否折叠机遇自己需求。

#### 二、 添加词库

词库转换工具 **rime_dict_manager** 是自带的，路径在 `/Library/Input Methods/Squirrel.app/Contents/MacOS` 下，可以写成 bash 脚本：

    DYLD_LIBRARY_PATH="/Library/Input Methods/Squirrel.app/Contents/Frameworks" "/Library/Input Methods/Squirrel.app/Contents/MacOS/rime_dict_manager" $@

以上保存为 **rime_dict_manager**，并 `chmod +x ./rime_dict_manager` 加上执行权限。

需要的最终词库格式如下：

    力量
    能力
    那你
    内容

词频信息，可有可无，如果需要词频，则需要以 tab 制表符来分割。如果你得到的第三方词库文件名为 **pyPhrase.dic**，则可以通过 opencc (通过 brew install opencc 安装) 转化为繁体后在转换成 kct 词库文件：

    opencc -i ./pyPhrase.dic -o ./pyPhrase.dic.new
    ./rime_dict_manager -i luna_pinyin ./pyPhrase.dic.new

该命令会生成 Rime 的词库，名为 **luna_pinyin.userdb.kct**，将 **luna_pinyin.userdb.kct** 拷贝到 **~/Library/Rime** 目录下下，重新部属下 Rime 即可。这种转换方式在 OS X 下重新部署 Rime 时，会在 **/Library/Rime/luna_pinyin.userdb** 文件夹下生成大量的 ldb 用户数据文件，通过 rime_dict_manager 转换的方法不推荐，因为在每次重新部署和同步的时候都需要花费大量的时间。

##### 外挂词库

如果词库的格式正确，推荐用外挂扩展词库的方式去实现，即直接用 opencc 转化成繁体格式输出为 **luna_pinyin.yourname.dict.yaml**：

    opencc -i ./pyPhrase.dic -o ./luna_pinyin.yourname.dict.yaml

用文本编辑器编辑 **luna_pinyin.yourname.dict.yaml** 文件，头部添加

    ---
    name: luna_pinyin.yourname
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    ...

随后在 **luna_pinyin.extended.dict.yaml** 文件中的 `import_tables` 下引入自己制作的词库，这种方式需要按照文章开头概述中的第 3、4 步骤去做。

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin
      - luna_pinyin.yourname
    ...

扩展词库文件，可以用我整理的 [【朙月拼音・简化字】擴充詞庫](https://github.com/iHavee/rime-files)。

当然你也可以自己在虚拟机中用工具 [imewlconverter](https://github.com/studyzy/imewlconverter) 去下载搜狗、百度的词库，再自己去转换。

当然完事后，不要忘记重新部署一下。

##### 词库总结

关于词库写的比较繁琐，这里总结下简要步骤，以简体中文为例。

###### 1. 创建 luna_pinyin_simp.custom.yaml

注意文件最后的

    translator:
      dictionary: luna_pinyin.extended

###### 2. 准备词库文件 luna_pinyin.extended.dict.yaml

    ---
    name: luna_pinyin.extended
    version: "your version"
    sort: by_weight
    use_preset_vocabulary: true
    inport_tables:
      - luna_pinyin
      - luna_pinyin.sogou
      ...

###### 3. 准备 sogou 词库文件

譬如下载我整理好的搜狗 40 万标准大词库： [【sogou for rime】](http://pan.baidu.com/s/1qYN6C3a)

忠告：词库并非越大越全越好，否则你准备生僻词翻N页后再开始选字吧。

#### 三、同步

关于 Rime 同步，我是直接用的 iCloud Drive，iCloud Drive 在用户目录的路径为 `~/Library/Mobile Documents/`。

打开 Rime 配置文件 installation.yaml，添加一行内容。

    installation_id: "Rime"

默认的同步文件夹即为 `~/Library/Rime/sync/`，你也可以添加 `sync_dir` 来自定义路径（绝对路径）。`installation_id: Rime` 为 `sync_dir` 的相对路径，在这里配置的最终路径为 `~/Library/Rime/sync/Rime`。

随后，我们在 iCloud Drive 建立一个 `Rime` 文件夹，并软链接到 `~/Library/Rime/sync/`。

    mkdir ~/Library/Mobile\ Documents/com~apple~CloudDocs/Rime
    ln -s ~/Library/Mobile\ Documents/com~apple~CloudDocs/Rime ~/Library/Rime/sync/

OK，重新部署，以及同步吧。如果你在同一个 Apple ID 的不同 OS X 之间同步，那么在其他不同 OS X 上只需要创建软链接一步即可，不过路径要换成自己的。
