---
title: 排查 Windows 无法完成系统配置的问题
titlesuffix: Azure Virtual Machines
description: 本文提供了一些步骤，用于解决 Sysprep 进程阻止启动 Azure VM 的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: bde091b4a4559c3574ee122d74574d1f9477f3fd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977042"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>排查 Windows 无法完成系统配置的问题

本文提供了解决以下问题的步骤： Sysprep 进程阻止启动 Azure 虚拟机 (VM) 。

## <a name="symptom"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 的屏幕截图时，你会看到屏幕截图显示 windows 安装程序正在启动服务时出现 "安装 windows" 错误。 此错误将显示消息：

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![图1显示 "安装 Windows 错误" 错误消息： "Windows 无法完成系统的配置。 若要尝试恢复配置，请重新启动计算机。 安装程序正在启动服务 "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>原因

当操作系统 (操作系统) 无法完成 [Sysprep 进程](/windows-hardware/manufacture/desktop/sysprep-process-overview)时，会导致此错误。 当你尝试初始启动通用化 VM 时，将发生此错误。 如果遇到此问题，请重新创建通用化映像，因为映像处于不可部署状态且无法恢复。

## <a name="solution"></a>解决方案

若要解决此问题，请遵循 Azure 指南，了解如何 [准备/捕获映像](../windows/upload-generalized-managed.md) 并准备新的通用映像。