---
layout: post
title: "给 Rime 添加第三方词库"
category: Mac
tags: [Rime]
---

#### 一、 概述

输入法是用户的基础工具，每天都用得到，但是现在的各类云输入法在方便用户的同时，敏感数据非加密传输等隐私问题就一直没有中断过，方便用户输入就是当今各商业输入法的一块遮羞布。最近一直看到圈里流传 Rime 输入法，就花了点时间折腾了一下。

![Rime](http://cdn.09hd.com/images/2015/05/rime.png)

OS X 下的用户配置文件在 **~/Library/Rime** 目录下，下面记录下使用 Rime 的一些个人配置。

<!-- more -->

##### 1. installation.yaml

```yaml
distribution_code_name: Squirrel
distribution_name: "鼠鬚管"
distribution_version: 0.9.26.1
install_time: "Mon May 27 10:56:16 2015"
installation_id: "RimeSync"
sync_dir: "/Users/Havee/Documents"
rime_version: 1.2.9
```

##### 2. squirrel.custom.yaml

```yaml
patch:
  us_keyboard_layout: true                 # 美式键盘布局
  show_notifications_via_notification_center: true
  style/color_scheme: light                # 选择配色方案
  style/horizontal: true                   # 候选条横向显示
  style/inline_preedit: true               # 启用内嵌编码模式，候选条首行不显示拼音
  style/corner_radius: 5                   # 窗口圆角半径
  style/border_height: 7                   # 窗口边界高度，大于圆角半径才生效
  style/border_width: 7                    # 窗口边界宽度，大于圆角半径才生效
  #style/line_spacing: 1                    # 候选词的行间距
  #style/spacing: 5                         # 非内嵌编码模式下，预编辑与候选词的间距
  #font_face: "Lucida Grande"               # 预选栏文字字体
  #style/label_font_face: "Lucida Grande"   # 预选栏编号字体
  style/font_point: 18                     # 预选栏文字字号
  style/label_font_point: 14               # 预选栏编号字号

  preset_color_schemes:
    light:                                                # 配色方案名称
      name: Register                                      # 作者名字
      author: "register <registerdedicated@gmail.com>"    # 作者
      text_color: 0x000000                                # 拼音行文字颜色，24位色值，16进制，BGR顺序
      candidate_text_color: 0x000000                      # 预选项文字颜色
      back_color: 0xFFFFFF                                # 背景色
      border_color: 0xE0B693                              # 边框色
      hilited_text_color: 0xFF6941                        # 高亮拼音 (需要开启内嵌编码)
      hilited_candidate_back_color: 0xFFFFFF              # 第一候选项背景背景色
      hilited_candidate_text_color: 0xFF6941              # 第一候选项文字颜色
      hilited_comment_text_color: 0xFF6941                # 注解文字高亮

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
  punctuator:
    import_preset: symbols
    full_shape:
      "\\": "、"
    half_shape:
      "\\": "、"

  recognizer:
    import_preset: default
    patterns:
      punct: "^/([0-9]+[a-z]*|[a-z]+)$"
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

  translator:
    dictionary: luna_pinyin.extended
  "speller/algebra/@before 0": xform/^([b-df-np-z])$/$1_/
```

##### 4. luna_pinyin.extended.dict.yaml

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin

**import_tables_** 是添加扩展词库用的，可以添加第三方的词库文件，譬如 **luna_pinyin.sogou.dict.yaml**，则添加格式如下

    ---
    name: luna_pinyin.extended
    version: "2015.05.27"
    sort: by_weight
    use_preset_vocabulary: true
    import_tables:
      - luna_pinyin
      - luna_pinyin.sogou
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

最后推荐一个 OS X 下的 Rime 设置工具：<https://github.com/neolee/SCU>

参考：<https://github.com/rime/home/wiki/UserGuide>
