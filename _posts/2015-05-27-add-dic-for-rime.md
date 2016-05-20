---
layout: post
title: "给 Rime 添加第三方词库"
category: Mac
tags: [Rime]
---

#### 一、 概述

输入法是用户的基础工具，每天都用得到，但是现在的各类云输入法在方便用户的同时，敏感数据非加密传输等隐私问题就一直没有中断过，方便用户输入就是当今各商业输入法的一块遮羞布。最近一直看到圈里流传 Rime 输入法，就花了点时间折腾了一下。

![Rime](http://cdn.09hd.com/images/2015/05/rime.png)

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
installation_id: "Rime-Sync"
sync_dir: "/opt/Rime"
rime_version: 1.2.9
```

##### 2. squirrel.custom.yaml

```yaml
patch:
  show_notifications_via_notification_center: true
  us_keyboard_layout: true                               # 美式键盘布局
  show_notifications_when: appropriate                   # 状态通知，适当，也可设为全开（always）全关（never）


  style:
    color_scheme: light                                  # 配色方案名称

  preset_color_schemes:
    light:
      name: register                                     # 作者名
      author: "register <registerdedicated@gmail.com>"   # 作者

      horizontal: true                                   # 候选条横向显示
      inline_preedit: true                               # 启用内嵌编码模式，候选条首行不显示拼音
      candidate_format: "%c\u2005%@\u2005"               # 用 1/6 em 空格 U+2005 来控制编号 %c 和候选词 %@ 前后的空间。

      corner_radius: 5                                   # 候选条圆角半径
      border_height: 7                                   # 窗口边界高度，大于圆角半径才生效
      border_width: 7                                    # 窗口边界宽度，大于圆角半径才生效
      back_color: 0xFFFFFF                               # 候选条背景色
      border_color: 0xE0B693                             # 边框色
      font_face: "PingFangSC-Regular"                    # 候选词字体
      font_point: 18                                     # 预选栏文字字号
      label_font_face: "PingFangSC-Light"                # 候选词编号字体
      label_font_point: 14                               # 预选栏编号字号
      candidate_text_color: 0x000000                     # 预选项文字颜色
      text_color: 0x000000                               # 拼音行文字颜色，24位色值，16进制，BGR顺序
      comment_text_color: 0x999999                       # 拼音等提示文字颜色
      hilited_text_color: 0xFF6941                       # 高亮拼音 (需要开启内嵌编码)
      hilited_candidate_text_color: 0xFF6941             # 第一候选项文字颜色
      hilited_candidate_back_color: 0xFFFFFF             # 第一候选项背景背景色
      hilited_comment_text_color: 0xFF6941               # 注解文字高亮

  app_options:
    com.blacktree.Quicksilver: &a
      ascii_mode: false
    com.googlecode.iterm2: *a
    com.alfredapp.Alfred: *a
    com.runningwithcrayons.Alfred-2: *a
    org.vim.MacVim: *a
    com.apple.Terminal: *a
```

##### 3. luna_pinyin_simp.custom.yaml

```yaml
patch:
  switches:
    - name: ascii_mode
      reset: 0
      states: ["中文", "西文"]
    - name: full_shape
      states: ["半角", "全角"]
    - name: extended_charset
      states: ["通用", "增廣"]
    - name: zh_simp
      reset: 1
      states: ["漢字", "汉字"]
    - name: ascii_punct
      states: ["。，", "．，"]

  simplifier:
    option_name: zh_simp

  engine:
    processors:
      - ascii_composer
      - recognizer
      - key_binder
      - speller
      - punctuator
      - selector
      - navigator
      - express_editor
    segmentors:
      - ascii_segmentor
      - matcher
      - abc_segmentor
      - punct_segmentor
      - fallback_segmentor
    translators:
      - punct_translator
      - table_translator@custom_phrase
      - reverse_lookup_translator
      - script_translator
    filters:
      - simplifier
      - uniquifier
      - cjk_minifier                                     #過濾拼音輸入法中的罕用字
  translator:
    enable_charset_filter: true                          #启用罕见字過濾

  "schema/dependencies/@next": easy_en                   # 加載 easy_en 依賴
  "engine/translators/@next": table_translator@english   # 載入翻譯英文的碼表翻譯器，取名爲 english

  english:                                               # english 翻譯器的設定項
    dictionary: easy_en
    spelling_hints: 9
    enable_completion: false
    enable_sentence: false
    initial_quality: -3

  translator:
    dictionary: luna_pinyin.extended
  speller:
    "algebra/@before 0": xform/^([b-df-hj-np-tv-z])$/$1_/

  punctuator:                                            # 符号快速输入和部分符号的快速上屏
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

  reverse_lookup:
    comment_format:
      - "xform/([nl])v/$1ü/"
    dictionary: stroke
    enable_completion: true
    preedit_format:
      - "xlit/hspnz/一丨丿丶乙/"
    prefix: "`"
    suffix: "'"
    tips: "〔筆畫〕"
```

##### 4. luna_pinyin.extended.dict.yaml

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin

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
      - luna_pinyin.cn_en
      - luna_pinyin.emoji
      ...

#### 二、 添加词库

词库转换工具 **rime_dict_manager** 是自带的，路径在 `/Library/Input Methods/Squirrel.app/Contents/MacOS` 下，可以写成 bash 脚本：

    DYLD_LIBRARY_PATH="/Library/Input Methods/Squirrel.app/Contents/Frameworks" "/Library/Input Methods/Squirrel.app/Contents/MacOS/rime_dict_manager" $@

以上保存为 **rime_dict_manager**，并 `chmod +x ./rime_dict_manager` 加上执行权限。

需要的最终词库格式如下：

    力量    li liang    1
    能力    neng li     1
    那你    na ni       1
    内容    nei rong    1
    ...

第三列是词频信息，可有可无，三列以 tab 制表符分割。如果你得到的第三方词库文件名为 **pyPhrase.dic**，则可以通过 opencc (通过 brew install opencc 安装) 转化为繁体后在转换成 kct 词库文件：

    opencc -i ./pyPhrase.dic -o ./pyPhrase.dic.new
    ./rime_dict_manager -i luna_pinyin ./pyPhrase.dic.new

该命令会生成 Rime 的词库，名为 **luna_pinyin.userdb.kct**，将 **luna_pinyin.userdb.kct** 拷贝到 **~/Library/Rime** 目录下下，重新部属下 Rime 即可。这种转换方式在 OS X 下重新部署 Rime 时，会在 **/Library/Rime/luna_pinyin.userdb** 文件夹下生成大量的 ldb 用户数据文件，通过 rime_dict_manager 转换的方法不推荐，因为在每次重新部署和同步的时候都需要花费大量的时间。

如果词库的格式正确，推荐用外挂扩展词库的方式去实现，即直接用 opencc 转化成繁体格式输出为 **luna_pinyin.yourname.dict.yaml**：

    opencc -i ./pyPhrase.dic -o ./luna_pinyin.yourname.dict.yaml

用文本编辑器编辑 **luna_pinyin.yourname.dict.yaml** 文件，头部添加

    ---
    name: luna_pinyin.yourname
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    ...

    釣魚島    diao yu dao      1
    黑瞎子島  hei xia zi dao   1
    南沙羣島  nan sha qun dao  1
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

扩展词库文件，可以用网友整理的 [【朙月拼音擴充詞庫】](https://bintray.com/rime-aca/dictionaries/luna_pinyin.dict)。或者在这里下载整理好的 [【sogou for rime】](http://pan.baidu.com/s/1jGrJbtc)。

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

譬如下载我整理好的： [【sogou for rime】](http://pan.baidu.com/s/1jGrJbtc)

如果不使用除 sogou 以外的所有扩展词库，那么直接略去第二步。同时，第一步的 dictionary 为 luna_pinyin.sogou，再加上第三步即可。

#### 三、同步

关于 Rime 同步，我是直接用的 iCloud Drive，iCloud Drive 在用户目录的路径为 `~/Library/Mobile Documents/`。

注意，创建同步目录的时候不能直接在 `~/Library/Mobile Documents/` 下创建，无法识别乃至无法同步的，正确的做法是：

- 1. Finder 侧边栏的 iCloud Drive 中创建一个 Rime 的同步文件夹 RimeSync
- 2. 以上创建同步文价夹后的路径在 `~/Library/Mobile Documents/com~apple~CloudDocs/` 下

由此，打开 Rime 配置文件 installation.yaml，添加两行内容来同步。

    installation_id: "Rime-Sync"
    sync_dir: "/opt/Rime"

恩，同步路径怎么不对，不着急，我是为了在不同 Mac OS X 之间同步，才如此的

    sudo mkdir /opt/Rime
    sudo chown -R yourname:yourgroup /opt/Rime                                              # 注意更改成你自己的用户与用户组
    mkdir /Users/Havee/Library/Mobile\ Documents/com~apple~CloudDocs/Rime-Sync              # 更改成你自己的同步盘地址，我使用的是 iCloud Drive
    ln -s /Users/Havee/Library/Mobile\ Documents/com~apple~CloudDocs/Rime-Sync /opt/Rime/   # 软链接到 installation.yaml 中的 sync_dir 地址

OK，重新部署，以及同步吧。

#### 最后

最后推荐一个 OS X 下的 Rime 设置工具：<https://github.com/neolee/SCU>

如果你想看我的配置，前往 <https://github.com/iHavee/rime-files>，你可以直接采用，不过需要注意的是 `installation.yaml` 中的一些配置修改成你自己的。

参考：<https://github.com/rime/home/wiki/UserGuide>

- 2016-05-20: 更新一些过时的做法
