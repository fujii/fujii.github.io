---
layout: post
title: "Karabiner で Windows 風キーバインド"
description: ""
category: 
tags: [macos, emacs]
---

private.xml

~~~xml
<?xml version="1.0"?>
<root>
    <item>
        <name>Private</name>
        <item>
            <identifier>private.remap.option_tab.terminal</identifier>
            <name>Option+Tab to Command+Tab in Terminal and Emacs</name>
            <only>TERMINAL, EMACS</only>
            <autogen>__KeyToKey__ KeyCode::TAB, MODIFIERFLAG_EITHER_LEFT_OR_RIGHT_OPTION, KeyCode::TAB, ModifierFlag::COMMAND_L</autogen>
	</item>
        <item>
            <identifier>private.remap.modifier.other</identifier>
            <name>not for Terminal, RD, VM and Emacs</name>
            <appendix>Option to Control</appendix>
            <appendix>Command to Option</appendix>
            <appendix>Control to Command</appendix>
            <appendix>Swap Command+Tab and Control+Tab</appendix>
            <not>TERMINAL,EMACS,VIRTUALMACHINE,REMOTEDESKTOPCONNECTION</not>
            <autogen>__KeyToKey__ KeyCode::TAB, MODIFIERFLAG_EITHER_LEFT_OR_RIGHT_CONTROL, KeyCode::TAB, ModifierFlag::COMMAND_L</autogen>
            <autogen>__KeyToKey__ KeyCode::TAB, MODIFIERFLAG_EITHER_LEFT_OR_RIGHT_COMMAND, KeyCode::TAB, ModifierFlag::CONTROL_L</autogen>
            <autogen>__KeyToKey__ KeyCode::OPTION_L, KeyCode::CONTROL_L</autogen>
            <autogen>__KeyToKey__ KeyCode::COMMAND_L, KeyCode::OPTION_L</autogen>
            <autogen>__KeyToKey__ KeyCode::CONTROL_L, KeyCode::COMMAND_L</autogen>
       </item>
    </item>
</root>
~~~

![karabiner.png]({{ BASE_PATH }}/assets/karabiner.png)
