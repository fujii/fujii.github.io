---
layout: post
title: "変換/無変換キーをCtrlやAltキーに割り当てる"
description: ""
tags: 
---

こちらの記事にキーボードレイアウトとオプションの追加方法が説明されている。
* [Who-T: User-specific XKB configuration - putting it all together](https://who-t.blogspot.com/2020/09/user-specific-xkb-configuration-putting.html)

ここではキーボードオプションを追加する。
以下のような内容で３つのファイルを作成すると、Gnome Tweaks にオプションが現れる。
オプションをオン/オフすると即座に反映される。

~~~
fujii@fedora $ cat ~/.config/xkb/rules/evdev
! option = symbols
  custom:fujii = +custom(fujii)

// Include the system 'evdev' file
! include %S/evdev
~~~

~~~
fujii@fedora $ cat ~/.config/xkb/rules/evdev.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xkbConfigRegistry SYSTEM "xkb.dtd">
<xkbConfigRegistry version="1.1">
  <optionList>
    <group allowMultipleSelection="true">
      <configItem>
        <name>custom</name>
        <description>custom options</description>
      </configItem>
      <option>
        <configItem>
          <name>custom:fujii</name>
          <description>Henkan/Muhenkan as Ctrl, Katakana/Hiragana as Alt</description>
        </configItem>
      </option>
    </group>
  </optionList>
</xkbConfigRegistry>
~~~

~~~
fujii@fedora $ cat ~/.config/xkb/symbols/custom
partial modifier_keys
xkb_symbols "fujii" {
  key <MUHE> { [ Control_L ] };
  key <HENK> { [ Control_R ] };
  modifier_map Control { <MUHE>, <HENK> };
  key <HKTG> { [ Alt_R ] };
};
~~~

![Gnome Tweaks の screenshot]({{ BASE_PATH }}/assets/thumb-ctrl.png)

## 親指コントロール

Emacs ユーザが減って親指コントロール派が減ってしまい寂しい限りです。

* [親指コントロール (たつをの ChangeLog) (2001-10-04)](https://chalow.net/2001-10-04-3.html)
* [親指Controlのすすめ](https://rcmdnk.com/blog/2017/10/10/computer-windows-mac-bash/)
* [プログラミングが捗る親指Ctrl - In Silico 創薬](https://www.insilico.jp/blog/2019/05/31/programming_thumb_ctrl_key/)
