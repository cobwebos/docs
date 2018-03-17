---
title: "Microsoft Azure 堆栈疑难解答 |Microsoft 文档"
description: "Azure 堆栈开发工具包 (ASDK) 疑难解答信息。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6ed3fb7c7c4de9edbf31fb2c47290e2e39ceadcd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Microsoft Azure 堆栈开发工具包 (ASDK) 疑难解答
本文档提供 ASDK 的常见故障排除信息。 如果遇到未记录的问题，请务必检查[Azure 堆栈 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)需进一步的帮助和信息。  

> [!IMPORTANT]
> 由于 ASDK 是评估环境，没有提供通过 Microsoft 客户支持服务 (CSS) 正式支持。

本部分针对故障排除问题提供的建议派生自多个来源，不保证能够解决具体的问题。 "按原样"提供了代码示例，不能保证预期的结果。 随着产品的不断改进，本部分的内容可能会频繁更新。

## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。  

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署结束时，PowerShell 会话仍保持打开状态，但不显示任何输出
此行为可能是选择 PowerShell 命令窗口后的默认行为。 开发工具包部署已成功完成，但选择窗口时，该脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。 按 ESC 键取消选择窗口，然后即会显示完成消息。

## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重启 Azure Stack 主机之后，某些 VM 可能不会自动启动。
将重新启动主机之后，可能会发现，Azure Stack 服务并非立即可用。 这是因为 Azure 堆栈[基础结构 Vm](asdk-architecture.md#virtual-machine-roles)并 RPs 采取一些时间来检查一致性，但最终将自动启动。

另外还可能发现，在重新启动 Azure Stack 开发工具包主机之后，租户 VM 不会自动启动。 这是一个已知问题，只需执行几个手动步骤就能让它们联机：

1.  在 Azure Stack 开发工具包主机上，从“开始”菜单启动“故障转移群集管理器”。
2.  选择群集“S-Cluster.azurestack.local”。
3.  选择“角色”。
4.  租户 VM 显示为“已保存”状态。 所有基础结构 VM 运行后，右键单击租户 VM，并选择“启动”以恢复该 VM。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已删除某些虚拟机，但仍在磁盘上看到 VHD 文件。 这是预期行为吗？
是的，这是预期行为。 设计此行为的原因如下：

* 删除 VM 时，不会删除 VHD。 磁盘是资源组中的独立资源。
* 删除存储帐户后，Azure 资源管理器会立即反映删除结果，但其中的磁盘仍保留在存储中，直到运行垃圾收集为止。

如果看到“孤立的”VHD，必须知道它们是否包含在已删除的存储帐户的文件夹中。 如果未删除存储帐户，则正常情况下，这些 VHD 仍在存储帐户中。

可以在[管理存储帐户](.\.\azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
可能需要最多 14 小时才会显示在门户中的回收容量。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，根据删除的数据量，我们无法保证运行垃圾收集器时可回收的空间量。

## <a name="next-steps"></a>后续步骤
[请访问 Azure 堆栈支持论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

