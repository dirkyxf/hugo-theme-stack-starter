---
title: "Windows 本地 AI 开发进展：PyTorch、llama.cpp 与 Windows ML"
description: "微软介绍 Windows ML 对 GGUF 与 llama.cpp 的实验性支持、原生 Runtime API，以及 Windows on Arm 上 PyTorch 和 Triton 的开发进展。"
date: 2026-10-08
categories:
  - AI
tags:
  - Windows ML
  - Local AI
  - GGUF
  - llama.cpp
  - PyTorch
  - Triton
---

微软 Foundry on Windows 团队于 2026 年 10 月 7 日发布了 Windows 本地 AI 开发更新。下面是对原文的中文译述与重点整理，内容涵盖 Windows ML、开源模型和开发工具链；具体功能仍以官方支持范围为准。

> 原文：[AI Development on Windows: from PyTorch and llama.cpp to Windows ML](https://devblogs.microsoft.com/foundry-on-windows/build-on-winml-oct-7-26/)（作者：Anastasiya Tarnouskaya、Michael Von Hippel、Tucker Burns）

## 这次更新的重点

微软希望让开发者可以沿用熟悉的开源模型和工具，在 Windows 上完成从实验、训练到本地推理的工作。此次更新主要包括：Windows ML 实验性接入 llama.cpp 和 GGUF；新增面向文本生成、语音识别的任务 API；预览新的 Windows 原生 Runtime API；以及扩展 Windows on Arm 上的 PyTorch、Triton 支持。

## 在 Windows ML 中运行 GGUF 和 llama.cpp

GGUF 社区和 llama.cpp 已成为许多开发者尝试新开源模型的常用选择。微软正在把 GGUF 支持加入 Windows ML：开发者可以从 Hugging Face 获取 GGUF 模型，通过 Windows ML 的统一接口在本机运行。该集成目前仍是实验性的，并与既有 ONNX 工作流并行。

微软也与 NVIDIA 及开源社区合作改进 llama.cpp 性能，包括 CUDA 内核优化与融合、改进 CPU/GPU 调度、权重重新打包和 CUDA Graphs。相关工作还涉及 Eagle-3、MTP、D-Flash2 等推测解码方式、多 GPU 执行、NVFP4、新模型架构和后端采样支持。

Windows ML 定位为 Windows 上统一的本地 AI 推理框架，可在 AMD、Intel、NVIDIA 和 Qualcomm 的 CPU、GPU、NPU 等硬件上运行模型。把推理放在本机，可能有助于降低延迟、让数据留在设备上，并避免按 token 计费的云端推理成本。配套的 [Windows ML CLI](https://aka.ms/winmlcli) 可用于转换、优化、编译和基准测试模型。

## 两个任务 API：文本生成与语音识别

新的 **Text Generation API** 可通过同一套简化接口运行 GGUF 或 ONNX 语言模型，并由 Windows ML 为模型选择相应的执行引擎，GGUF 模型可由 llama.cpp 驱动。接口还提供兼容 OpenAI 的本地端点，因此开发者可以继续使用熟悉的 OpenAI SDK，连接设备上的模型进行原型开发。

首批任务 API 还包括 **Speech Recognition API**：它使用 ONNX 格式的 Whisper 模型进行语音转写。两种 API 可以串联，例如先把语音转成文字，再把文本交给本地 GGUF 语言模型处理。

官方文档：[文本生成 API](https://learn.microsoft.com/windows/ai/new-windows-ml/runtime/text-generation) · [语音识别 API](https://learn.microsoft.com/windows/ai/new-windows-ml/runtime/speech-recognition)

## 更底层的 Windows ML Runtime API

新的 Windows 原生 **Runtime API** 目前处于实验性预览阶段，为需要更细粒度控制的开发者提供更接近系统底层的能力。熟悉的 ONNX Runtime API 仍会继续支持；两套 API 并行存在，开发者可以先沿用现有方案，再按需采用原生 Runtime 路径。

Runtime API 的主要能力包括：直接向模型传入图像、视频帧、音频缓冲区和文本等 Windows 原生数据类型，减少手工预处理和格式转换；把多个模型串成确定性的流水线，并明确指定每个阶段在 CPU、GPU 或 NPU 上执行；以及提前加载、编译模型，生成可直接运行的产物，以加快启动并稳定应用的设备与执行策略。

较高层的文本生成和语音识别 API 也构建在 Runtime API 之上，因此可以先用简单接口，再在需要时下探到更细粒度的运行控制。可参考 [Runtime API 概览](https://learn.microsoft.com/windows/ai/new-windows-ml/runtime/overview) 和[示例](https://aka.ms/winml-runtime-samples)。

## PyTorch、Triton 与从训练到部署的流程

微软还在扩展 Windows 上的开源 AI 开发环境。PyTorch 已提供官方原生 Windows Arm64 CPU 版本，NVIDIA 也为受支持的硬件发布支持 CUDA 的 Windows Arm64 软件包。Windows 版 Triton 则为受支持的显卡提供 `triton.jit`、`torch.compile` 和自定义 GPU 内核等能力，Windows Arm64 上的编译器与发行版工作也在推进。

文章展示的开发流程是：用 PyTorch 训练或微调模型；通过 `torch.compile` 配合 Triton 生成并融合 GPU 内核；把模型计算图导出为可移植的 ONNX；再用 Windows ML CLI 分析目标设备、执行优化或量化、编译模型，并进行性能测试。也就是说，部署时带走的是模型图，而不是训练或编译过程中生成的临时 GPU 内核。CLI 可以协助检查执行提供程序、应用兼容的图优化和算子融合，并产出模型及应用构建配置。

微软表示，接下来会继续与开源维护者、硬件伙伴及 Python/AI 社区合作，重点改善原生软件包覆盖、内核支持、安装体验、运行性能，以及 Windows x64 和 Windows on Arm 之间的支持一致性。开发者也可以通过 [Windows ML GitHub](https://github.com/microsoft/WindowsML) 提交希望支持的模型、设备和工作流。

## 总结

这次更新的核心，是把 Windows 本地 AI 的路径连接得更完整：用 llama.cpp 和 GGUF 运行开源模型，用 PyTorch 与 Triton 开发、优化模型，再借助 ONNX 和 Windows ML 将模型带入应用；高层任务 API 负责简化常见调用，Runtime API 则面向需要精细控制的场景。

需要注意的是，文章介绍的 llama.cpp 集成和 Windows 原生 Runtime 能力都带有实验性标记。微软建议在用于生产环境前，先查看支持的场景和已知限制，并在目标 Windows 设备上验证模型与工作负载。
