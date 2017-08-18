---
layout: post
title: 解决mindmanager已停止工作的问题
category: 技术
keywords: java
---

1. 把文件后缀 mmap修改为 zip。
2. 找一个编辑器打开 Document.xml，找到你的文件位置信息，比如文件在桌面的，你会找到
DocumentPath="\\PC\<font color="blue">C\Users\roolg.ge\Desktop</font>\~$Mind.mmap.~$save"。
3. 把蓝色字(C\Users\....)修改为你的新位置，保存，关闭编辑器。
4. 现在把你修改完的 Document.xml 添加到刚刚那个 .zip文件里。
5. 接着把.zip 修改为 .mmap。