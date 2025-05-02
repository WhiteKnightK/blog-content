---
title: 这是文章标题
date: 2024-05-30
author: 你的名字
categories: ["分类1", "分类2"]
excerpt: 这是文章摘要，简短描述文章内容
---

# 这是文章正文

这里是文章内容...
asdpoamdapomsdpoasmpodmaspdomapsodmpoasmpdpoasdda
## Introduction

Smart contract auditing is often described as finding bugs in code (after all, a bug report is the expected output).

But many of you may have already realized this:

> Bugs are rarely just coding errors. They're broken assumptions. They're mismatches between the user's *expectations*, what the developer *intended*, and what the code *actually does*.

It's been shown time and time again: checklist-driven reviews are never enough. All top auditors eventually drop them. Instead, to uncover critical vulnerabilities and truly understand the code, they shift—consciously or not—toward a **spec thinking** mindset. One where checklists are made on the fly, born from uncovering assumptions and implicit rules.

This article introduces a minimalist but powerful framework I came up with, resulting from my years of work with Certora: **The Three Prompts of Spec Thinking**. It's a way to analyze code through a threat modeling lens. I hope it'll serve you well!

## What Even Is a Smart Contract (From First Principles)?

An **asset management system**, where **actors** interact to gain specific **benefits** through **actions**.

### What's an ASSET?

Money, power, or ownership assigned to a user.

👉 Try this cue: *"If it's something that can be transferred, it's an asset."*.

### What's an ACTOR?

Any interactooor with a smart contract:

* Other protocols (dependencies)
* Other users (permissioned or not)
* Yourself (as an attacker, alt account, or regular user)
* The protocol itself (`address(this)`)

👉 Try this cue: *If it can be identified by an `address`, it's an actor.*

### What are ACTIONS?

An **action** is when an actor initiates interaction with the system. This triggers some logic that takes in:

* Input parameters
* State variables
* External dependencies

…and produces:

* State changes (on the Smart Contract itself or on external dependencies, like balances)
* Returned values
* Either a success or a revert

The flow may span multiple steps, touch multiple assets, or impact multiple actors.

🔗 I recommend re-reading my post here: [https://justdravee.github.io/posts/thread-state-machine/](https://justdravee.github.io/posts/thread-state-machine/)
