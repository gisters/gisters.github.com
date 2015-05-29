---
layout: post
title: "给 Rime 添加第三方词库"
category: Mac
tags: [Rime]
---

#### 一、 概述

OS X 下的用户配置文件在 **~/Library/Rime** 目录下，下面记录下使用 Rime 的一些个人配置。

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

<!-- more -->
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

```yaml
---
name: luna_pinyin.extended
version: "2015.05.27"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
```

**import_tables_** 是添加扩展词库用的，可以添加第三方的词库文件，譬如 **luna_pinyin.sogou.dict.yaml**，则添加格式如下

```yaml
---
name: luna_pinyin.extended
version: "2015.05.27"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.sogou
...
```

#### 二、 添加词库

词库转换工具 **rime_dict_manager** 是自带的，路径在 `/Library/Input Methods/Squirrel.app/Contents/MacOS` 下，可以写成 bash 脚本：

```bash
DYLD_LIBRARY_PATH="/Library/Input Methods/Squirrel.app/Contents/Frameworks" "/Library/Input Methods/Squirrel.app/Contents/MacOS/rime_dict_manager" $@
```

以上保存为 **rime_dict_manager**，并 `chmod +x ./rime_dict_manager` 加上执行权限。

需要的最终词库格式如下：

```
力量    li liang    1
能力    neng li     1
那你    na ni       1
内容    nei rong    1
……
```

第三列是词频信息，可有可无。如果你得到的第三方词库文件名为 **pyPhrase.dic**，则转换：

    ./rime_dict_manager -i luna_pinyin ./pyPhrase.dic

该命令会生成 Rime 的词库，名为 **luna_pinyin.userdb.kct**，将 **luna_pinyin.userdb.kct** 拷贝到 **~/Library/Rime** 目录下下，重新部属下 Rime 即可，这种方式每次部署都需要重编码，每次时间都比较长。

推荐不要经过上面那步转换，如果格式正确，直接改名为 **luna_pinyin.sogou.dict.yaml** (其中的 sogou 你可以改为其他名，譬如 other)，随后在 **luna_pinyin.extended.dict.yaml** 文件中的 `import_tables` 下按格式添加，这种方式需要按照文章开头概述中的第 3、4 步骤去做。

```yaml
---
name: luna_pinyin.extended
version: "2015.05.27"
sort: by_weight
use_preset_vocabulary: true
import_tables:
  - luna_pinyin
  - luna_pinyin.sogou
  - luna_pinyin.other
...
```

扩展词库文件，可以用网友整理的，也可以自己在虚拟机中用工具 [imewlconverter](https://github.com/studyzy/imewlconverter) 去下载搜狗、百度的词库，再自己去转换。

最后推荐一个 OS X 下的 Rime 设置工具：<https://github.com/neolee/SCU>

参考：<https://github.com/rime/home/wiki/UserGuide>
