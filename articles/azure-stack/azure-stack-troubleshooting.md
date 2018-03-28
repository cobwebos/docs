---
title: Microsoft Azure 堆栈疑难解答 |Microsoft 文档
description: Azure Stack 故障排除。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b63fdd630647cc970a2d935619b4d3f16b8c0375
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure 堆栈故障排除

*适用于：Azure Stack 开发工具包*

本文档提供 Azure Stack 的常见故障排除信息。 

由于 Azure Stack 技术开发工具包以评估环境的形式提供，因此 Microsoft 客户支持服务不会提供官方支持。 如果本文未阐述你所遇到的问题，请务必查看 [Azure Stack MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)以获取更多帮助和信息。  

本部分针对故障排除问题提供的建议派生自多个来源，不保证能够解决具体的问题。 代码示例按原样提供，不保证生成预期的结果。 随着产品的不断改进，本部分的内容可能会频繁更新。

## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果安装期间发生失败，可以使用部署脚本的 -rerun 选项从失败的步骤重新开始部署。  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>部署结束时，PowerShell 会话仍保持打开状态，但不显示任何输出
此行为可能是选择 PowerShell 命令窗口后的默认行为。 开发工具包部署实际成功，但选择窗口时，脚本已暂停。 可以通过在命令窗口的标题栏中查找“select”一词，来验证安装是否已完成。  按 ESC 键取消选择窗口，然后即会显示完成消息。

## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
在 Azure Stack 中部署 VM 之前，必须先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重启 Azure Stack 主机之后，某些 VM 可能不会自动启动。
将重新启动主机之后，可能会发现，Azure Stack 服务并非立即可用。  这是因为 Azure Stack [基础结构 VM](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) 与 RP 需要花费一点时间来检查一致性，但最终会自动启动。

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

可以在[管理存储帐户](azure-stack-manage-storage-accounts.md)中详细了解如何配置保留阈值和按需回收。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
回收的容量最长可能需要在 14 小时后才显示在门户中。 空间回收取决于多种因素，包括块 Blob 存储中内部容器文件的用量百分比。 因此，删除的数据量无法保证垃圾收集器运行时可回收的空间量。

## <a name="windows-azure-pack-connector"></a>Windows Azure 包连接器
* 如果在部署 Azure Stack 开发工具包之后更改 azurestackadmin 帐户的密码，则不再能够配置多云模式。 因此，它将不能连接到目标 Windows Azure Pack 环境。
* 设置多云模式之后：
    * 用户只有在重置门户设置之后，才能看到仪表板。 （在用户门户中，单击门户设置图标（右上角的齿轮图标）。 在“还原默认设置”下，单击“应用”。）
    * 仪表板标题可能不显示。 如果发生此问题，必须手动添加标题。
    * 首次将某些磁贴添加到仪表板时，它们可能无法正常显示。 若要解决此问题，请刷新浏览器。



