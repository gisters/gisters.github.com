---
layout: post
title: "Firefox 重启脚本"
description: "脚本的作用：在app menu菜单下增加一个restart firefox项，与快捷键重启firefox。"
keywords: firefox, restart, 重启, 脚本
category: Internet
tags: [Firefox, UserChrome]
---
{% include JB/setup %}

脚本的作用：在 `app menu` 菜单下增加一个 `restart firefox` 项，与快捷键重启 firefox。

`appmenu` 上居然没有了，快捷键也冲突，所以改了下，`ctrl_shift_z` 为重启，`appmenu` 上也有重启菜单了，很简单的修改。<!-- more -->

```javascript
// ==UserScript==
// @name         Restart Firefox
// @namespace    http://www.xuldev.org/
// @description  Adds Restart menu and shortcut key.
// @include      main
// @author       Gomita
// @modify       Havanna
// @version      1.0.20100927
// @homepage     http://www.xuldev.org/misc/ucjs.php
// ==/UserScript==
 
function ucjsRestartApp() {
    var appStartup = Cc["@mozilla.org/toolkit/app-startup;1"]
                     .getService(Ci.nsIAppStartup);
    appStartup.quit(appStartup.eRestart | appStartup.eAttemptQuit);
}
 
(function() {
    var overlay =
        <overlay xmlns="http://www.mozilla.org/keymaster/gatekeeper/there.is.only.xul">
            <commandset id="mainCommandSet">
                <command id="cmd_RestartApp" oncommand="ucjsRestartApp();" />
            </commandset>
            <keyset id="mainKeyset">
                <key id="key_RestartApp" key="Z" modifiers="accel,shift" command="cmd_RestartApp" />
            </keyset>
            <menu id="appmenuPrimaryPane">
                <menuitem label="Restart" insertbefore="appmenu-quit"
                          command="cmd_RestartApp" />
            </menu>
            <menu id="menu_FilePopup">
                <menuitem label="Restart" insertbefore="menu_FileQuitItem"
                          key="key_RestartApp" command="cmd_RestartApp" />
            </menu>
        </overlay>;
    overlay = "data:application/vnd.mozilla.xul+xml;charset=utf-8," + encodeURI(overlay.toXMLString());
    document.loadOverlay(overlay, null);
})();
```