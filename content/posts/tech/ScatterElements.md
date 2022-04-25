---
title: "Custom Operator Development -- ScatterElements"
date: 2022-04-24T19:34:50+08:00
draft: false
author: Jian
tags: ["TensorRT"]
---

In the project I have confronted with the problem that we do not have the built-in operator for add_index() function in PyTorch while deploying the model in embedded system with TensorRT. So I need to write a customable operator for ScatterElements operation. In this blog I documented the development process for custom oprator.  

# Custom Operator Introduction
