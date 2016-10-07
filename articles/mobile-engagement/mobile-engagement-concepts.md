<properties
	pageTitle="Mobile Engagement 概念 | Microsoft Azure"
	description="Azure Mobile Engagement 概念"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />

# Azure Mobile Engagement 概念

Mobile Engagement 定义了所有受支持平台的一些常见概念。本文简要介绍了这些概念。

不熟悉 Mobile Engagement 的用户可先阅读本文。另外，请务必阅读所用平台的特定文档，了解本文所述概念的更多详细信息和示例，以及可能的限制。

## 设备和用户
Mobile Engagement 标识用户的方式是为每台设备生成一个唯一标识符。该标识符称为设备标识符（或 `deviceid`）。这种生成方式导致同一设备上所有正在运行的应用程序共享同一设备标识符。

这其中隐含的意义是，Mobile Engagement 认为一台设备只能属于一个用户，因此用户和设备是等效概念。

## 会话和活动
会话是指对一个用户所执行的应用程序进行一次使用，由用户开始，由用户停止。

活动是指对一个用户所执行的应用程序的给定子部件（通常是一个屏幕，但也可能是适用于应用程序的任何对象）进行一次使用。

用户一次只能执行一个活动。

活动按名称（仅限 64 个字符）标识，可以选择性地嵌入一些额外的数据（仅限 1024 字节）。

会话按用户所执行的一系列活动自动计算。会话从用户开始其第一个活动开始，至用户完成其最后一个活动结束。也就是说，会话不需要显式启动或停止。与之相反，活动是显式启动或停止的。如果没有报告活动，则不会报告会话。

## 事件
事件用于报告即时操作（例如用户按下了按钮或阅读了文章）。

事件可能与当前会话有关，可能与正在运行的作业有关，也可能是独立的事件。

事件按名称（仅限 64 个字符）标识，可以选择性地嵌入一些额外的数据（仅限 1024 字节）。

## 错误
错误用于报告应用程序正确检测到的问题（例如用户操作不正确，或者 API 调用失败）。

错误可能与当前会话有关，可能与正在运行的作业有关，也可能是独立的错误。

错误按名称（仅限 64 个字符）标识，可以选择性地嵌入一些额外的数据（仅限 1024 字节）。

## 作业
作业用于报告有一定持续时间（例如 API 调用的持续时间、广告的显示时间、后台任务的持续时间或用户操作的持续时间）的操作。

作业与会话无关，因为任务可以在后台执行，不需任何用户交互。

作业按名称（仅限 64 个字符）标识，可以选择性地嵌入一些额外的数据（仅限 1024 字节）。

## 崩溃
崩溃是由 Mobile Engagement SDK 自动发出的信号，用于报告应用程序故障，因应用程序检测不到的问题而引发。

## 应用程序信息
应用程序信息（或应用信息）用于标记用户，也就是说，将某些数据与应用程序的用户关联起来（这类似于 Web Cookie，区别是应用信息存储在 Azure Mobile Engagement 平台的服务器端）。

可通过 Mobile Engagement SDK API 或 Mobile Engagement 平台的设备 API 注册应用信息。

应用信息是与设备关联的键值对。键是应用信息的名称（仅限 64 个 ASCII 字符，包含字母 [a-zA-Z]、数字 [0-9] 和下划线 [\_]）。值（仅限 1024 个字符）可以是任意字符串、整数、日期 (yyyy-MM-dd) 或布尔值（true 或 false）。

可以将任意数量的应用信息与设备关联，但需符合 Mobile Engagement 定价条款所定义的限制。对于一个给定的键，Mobile Engagement 仅记录所设置的最新值（没有历史记录）。设置或更改应用信息的值会强制 Mobile Engagement 重新评估对该应用信息设置的受众条件（如果有），这意味着可以使用应用信息触发实时推送。

## 额外数据
额外数据（或附加数据）是可以附加到事件、错误、活动和作业的任意数据。

附加数据有着类似于 JSON 对象的结构：由键值对树组成。键仅限 64 个 ASCII 字符，包含字母 [a-zA-Z]、数字 [0-9] 和下划线 [\_]，而附加数据的总大小仅限 1024 个字符（以 JSON 格式被 Mobile Engagement SDK 编码过）。

包含键值对的整个树以 JSON 对象形式存储。不过，只能将第一级键值对进行分解，使之可供 Segments 之类的某些高级函数直接访问（例如，可以轻松地定义一个名为“SciFi 球迷”的类型，其中包含的所有用户在上个月至少发送了 10 次名为“content\_viewed”且额外键“content\_type”已设置为“scifi”值的事件）。因此，强烈建议仅发送由简单的键值对列表组成且使用标量值（例如，字符串、日期、整数或布尔值）的附加数据。

## 后续步骤

- [Windows Universal SDK overview for Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)（用于 Azure Mobile Engagement 的 Windows Universal SDK 概述）
- [Windows Phone Silverlight SDK overview for Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)（用于 Azure Mobile Engagement 的 Windows Phone Silverlight SDK 概述）
- [iOS SDK for Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)（用于 Azure Mobile Engagement 的 iOS SDK）
- [Android SDK for Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)（用于 Azure Mobile Engagement 的 Android SDK）

<!---HONumber=AcomDC_0921_2016-->