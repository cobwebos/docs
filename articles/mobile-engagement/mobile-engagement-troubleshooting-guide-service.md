---
title: "Azure Mobile Engagement 故障排除指南 - 服务"
description: "Azure Mobile Engagement 故障排除指南"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8b4275da-c0b4-4690-824a-48e9d7a1fc6e
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 01862d158f4771989ae6eb3b20fe8e080123a231
ms.lasthandoff: 11/17/2016


---
# <a name="troubleshooting-guide-for-service-issues"></a>服务问题故障排除指南
以下是在 Azure Mobile Engagement 运行时可能会遇到的问题。

## <a name="service-outages"></a>服务中断
### <a name="issue"></a>问题
* 似乎是由 Azure Mobile Engagement 服务中断引起的问题。

### <a name="causes"></a>原因
* 似乎是由 Azure Mobile Engagement 服务中断引起的问题可能由几个不同的问题引起：
  * 最初在所有 Azure Mobile Engagement 上出现的独立的系统化问题
  * 服务器中断引起的已知问题（并非始终显示在服务器状态中）：
  * 计划延迟、目标错误、徽章更新问题、统计信息停止收集、推送停止工作、API 停止工作、无法创建新应用或用户、DNS 错误和设备上的 UI、API 或应用中的超时错误。
  * 云依赖关系中断 [Azure 服务状态](http://status.azure.com/)
  * 推送通知服务 (PNS) 依赖关系中断
  * 应用商店中断

1) 要测试问题是否是系统性的，你可以测试不同位置的相同功能

* Azure Mobile Engagement 集成应用程序
* 测试设备
* 测试设备 OS 版本
* 活动
* 管理员用户帐户
* 浏览器（IE、Firefox、Chrome 等）
* 计算机

2) 要测试问题是否仅影响 UI 或 API：

* 在 Azure Mobile Engagement UI 和 Azure Mobile Engagement API 中测试相同的功能。

3) 要测试是否是移动电话网络的问题：

* 在通过 WIFI 连接到 Internet 时以及通过 3G 移动电话网络连接时进行测试。
* 确认你的防火墙未阻止任何 Azure Mobile Engagement IP 地址或端口。

4) 要测试是否是设备的问题：

* 测试你的设备是否能够使用另一个 Azure Mobile Engagement 集成应用连接到 Azure Mobile Engagement。
* 测试你可以在 Azure Mobile Engagement UI 中看到电话中生成的事件、作业和崩溃。 
* 测试是否可以将推送通知从 Azure Mobile Engagement UI 发送到基于设备 ID 的设备。 

5) 要测试是否是应用的问题：

* 在仿真程序中安装和测试应用程序，而不是在物理设备中：

6) 要测试是否是 OS 升级到最终用户设备的问题，这需要 SDK 升级来解决：

* 在具有不同 OS 版本的不同设备上测试应用程序。
* 确认你使用的是最新版本的 SDK。

## <a name="connectivity-and-incorrect-information-issues"></a>连接和不正确信息的问题
### <a name="issue"></a>问题
* 登录 Azure Mobile Engagement UI 时出现的问题。
* Azure Mobile Engagement API 的连接错误。
* 通过设备 API 上传应用信息标记时出现的问题。
* 从 Azure Mobile Engagement 下载日志或导出的数据时出现的问题。
* Azure Mobile Engagement UI 中显示的信息不正确。
* Azure Mobile Engagement 日志中显示的信息不正确。

### <a name="causes"></a>原因
* 确认你的用户帐户有足够的权限来执行该任务。
* 确认此问题不仅限于一台计算机或你的本地网络。
* 确认 Azure Mobile Engagement 没有已报告的中断。
* 确认你的应用信息标记文件遵循以下所有规则：
  * 仅使用 UTF8 字符集（不支持 ANSI 字符集）。
  * 使用逗号“,”作为分隔符（你可以打开服务请求，请求将 .csv 分隔符从逗号“,”更改为另一个字符，例如分号“;”）。
  * 布尔值“true”和“false”都使用小写。
  * 使用的文件小于最大文件大小 (35MB)。


