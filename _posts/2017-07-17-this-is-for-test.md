---
layout: post
title: Go Development Environment
tags: [TexturePacker, Cocos2d]
---

Recently, I’ve started to work with the Go language. One initial point of confusion for me was how to use workspaces. They work a little different than other frameworks and languages.

In simple terms, a workspace sets the context for running the go tool. Go is implemented by a suite of binaries similar to GCC. There is a compiler, linker and other tools for managing specific aspects of the Go workflow. On top of all of this is a tool called go that lets you use much simpler meta tasks like build, install, and test. For the high level tool to operate, it needs to have a context to work within. This includes where to find code and dependencies, where to build and install components, and generally set the state of the world.

Recently, I’ve started to work with the Go language. One initial point of confusion for me was how to use workspaces. They work a little different than other frameworks and languages.

In simple terms, a workspace sets the context for running the go tool. Go is implemented by a suite of binaries similar to GCC. There is a compiler, linker and other tools for managing specific aspects of the Go workflow. On top of all of this is a tool called go that lets you use much simpler meta tasks like build, install, and test. For the high level tool to operate, it needs to have a context to work within. This includes where to find code and dependencies, where to build and install components, and generally set the state of the world.

<!--more-->

#### Install cocos2d-x

Recently, I’ve started to work with the Go language. One initial point of confusion for me was how to use workspaces. They work a little different than other frameworks and languages.

In simple terms, a workspace sets the context for running the go tool. Go is implemented by a suite of binaries similar to GCC. There is a compiler, linker and other tools for managing specific aspects of the Go workflow. On top of all of this is a tool called go that lets you use much simpler meta tasks like build, install, and test. For the high level tool to operate, it needs to have a context to work within. This includes where to find code and dependencies, where to build and install components, and generally set the state of the world.

```
cd cocos2d-x-3.9
python setup.py
```

Recently, I’ve started to work with the Go language. One initial point of confusion for me was how to use workspaces. They work a little different than other frameworks and languages.

In simple terms, a workspace sets the context for running the go tool. Go is implemented by a suite of binaries similar to GCC. There is a compiler, linker and other tools for managing specific aspects of the Go workflow. On top of all of this is a tool called go that lets you use much simpler meta tasks like build, install, and test. For the high level tool to operate, it needs to have a context to work within. This includes where to find code and dependencies, where to build and install components, and generally set the state of the world.


#### Create The First Game

Recently, I’ve started to work with the Go language. One initial point of confusion for me was how to use workspaces. They work a little different than other frameworks and languages.

In simple terms, a workspace sets the context for running the go tool. Go is implemented by a suite of binaries similar to GCC. There is a compiler, linker and other tools for managing specific aspects of the Go workflow. On top of all of this is a tool called go that lets you use much simpler meta tasks like build, install, and test. For the high level tool to operate, it needs to have a context to work within. This includes where to find code and dependencies, where to build and install components, and generally set the state of the world.


```
cocos new -l cpp -d ~/cocos2d-x-games Cocos2dx-SpriteSheetTutorial
```
