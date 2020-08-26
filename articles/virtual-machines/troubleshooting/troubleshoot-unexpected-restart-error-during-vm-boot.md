---
title: 操作系统启动-计算机意外重新启动或遇到意外错误
description: 本文提供了一些步骤，用于解决 VM 在安装 Windows 时遇到意外重新启动或错误的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87036176"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>操作系统启动-计算机意外重新启动或遇到意外错误

本文提供了解决在安装 Windows 时虚拟机（VM）遇到意外重新启动或错误的问题的步骤。

## <a name="symptom"></a>症状

当你使用[启动诊断](./boot-diagnostics.md)来查看 VM 的屏幕截图时，你会看到屏幕截图显示 Windows 安装失败，并出现以下错误：

**计算机意外重启或遇到意外错误。Windows 安装无法继续。若要安装 Windows，请单击 "确定" 以重新启动计算机，然后重新启动安装。**

![Windows 安装过程中出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 若要安装 Windows，请单击 "确定" 以重新启动计算机，然后重新启动安装。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows 安装安装程序正在启动服务时出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 若要安装 Windows，请单击 "确定" 以重新启动计算机，然后重新启动安装。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

计算机正在尝试初始启动[通用化映像](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)，但由于正在处理自定义应答文件（unattend.xml）而遇到问题。 Azure 中不支持自定义应答文件。 

答案文件是一个特殊的 XML 文件，其中包含要在安装 Windows Server 操作系统的过程中自动执行的配置设置的设置定义和值。 配置选项包括有关如何对磁盘进行分区、在何处查找要安装的 Windows 映像的说明、要应用的产品密钥和其他你想要运行的命令。

在 Azure 中，不支持自定义应答文件。 如果使用选项指定了自定义**Unattend.xml**文件 `sysprep /unattend:<your file’s name>` ，则会发生此错误。

在 Azure 中，使用**Sysprep.exe**中的 "**进入系统全新体验（OOBE）** " 选项，或使用 `sysprep /oobe` 而不是 Unattend.xml 文件。

此问题通常是在将**Sysprep.exe**与本地 VM 一起使用以将通用 vm 上传到 Azure 时创建的。 在这种情况下，你可能还会对如何正确上传一般化 VM 感兴趣。

## <a name="solution"></a>解决方案

### <a name="replace-unattended-answer-file-option"></a>替换无人参与应答文件选项

当映像已准备好在 Azure 中使用，但它使用的是 Azure 中不支持的自定义应答文件，并且你将**SYSPREP**与类似于以下命令的标志一起使用时，就会出现这种情况：

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- 在前面的命令中， `<NameOfYourAnswerFile.XML>` 将替换为你的文件的名称。

若要解决此问题，请遵循 Azure 指南，了解如何[准备/捕获映像](../windows/upload-generalized-managed.md)并准备新的通用映像。 在 sysprep 期间，请不要使用 " `/unattend:<answerfile>` 标志"。 请改用下面的标志：

`sysprep /oobe /generalize /shutdown`

- **全新体验**（OOBE）是 Azure vm 支持的设置。

您还可以通过选择下面显示的选项，使用**系统准备工具 GUI**来完成上述命令的相同任务：

- 输入全新体验
- 通用化
- 关机
 
![选择了 OOBE、通用化和关闭选项的系统准备工具窗口。](./media/unexpected-restart-error-during-vm-boot/3.png)
