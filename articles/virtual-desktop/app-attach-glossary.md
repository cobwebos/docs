---
title: Windows 虚拟桌面 .MSIX 应用附加术语表-Azure
description: .MSIX 应用附加术语和概念的词汇表。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f3cc8495f673c8b428aa9e6ace2747a70c5b0847
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556220"
---
# <a name="msix-app-attach-glossary"></a>.MSIX 应用附加术语表

本文介绍与 .MSIX 应用附加相关的关键术语和概念的定义列表。

## <a name="msix-container"></a>.MSIX 容器

.MSIX 容器是运行 .MSIX 应用的位置。 若要了解详细信息，请参阅 [.msix 容器](/windows/msix/msix-container)。

## <a name="msix-application"></a>.MSIX 应用程序 

存储在 .MSIX 文件中的应用程序。

## <a name="msix-package"></a>.MSIX 包 

.MSIX 包是 .MSIX 文件或应用程序。

## <a name="msix-share"></a>.MSIX 共享

.MSIX 共享是保存扩展的 .MSIX 包的网络共享。 .MSIX 共享支持 SMB 3 或更高版本。 应用程序从该 .MSIX 共享进行过渡，无需将应用程序文件移到系统驱动器。

## <a name="repackage"></a>重新打包

重新打包将使用 .MSIX 打包工具 () ，从而将其转换为 .MSIX。 有关详细信息，请参阅 [.Msix 打包工具概述](/windows/msix/packaging-tool/tool-overview)。

## <a name="expand"></a>展开

扩展 .MSIX 包是一个多步骤过程。 它采用 .MSIX 文件，并将其内容置于 VHD (x) 或 CIM 文件中。 

展开 .MSIX 包：

1. 获取 .MSIX 包 (.MSIX 文件) 。
2. 将 .MSIX 文件重命名为 .zip 文件。
3. 将生成的 .zip 文件解压缩到文件夹中。
4. 创建与该文件夹大小相同的 VHD。
5. 装载 VHD。
6. 初始化磁盘。
7. 创建分区。
8. 格式化分区。
9. 将解压缩的内容复制到 VHD 中。
10. 使用 MSIXMGR 工具可对 VHD 的内容应用 Acl。
11. 卸载 VHD (x) 或 [CIM](#cim)。

## <a name="upload-an-msix-package"></a>上传 .MSIX 包 

上传 .MSIX 包需要将包含扩展的 .MSIX 包的 VHD (x) 或 [CIM](#cim) 上传到 .msix 共享。

在 Windows 虚拟桌面中，每个 .MSIX 共享发生一次上传。 上传包后，同一订阅中的所有主机池都可以引用它。

## <a name="publish-an-msix-package"></a>发布 .MSIX 包

在 Windows 虚拟桌面中，发布 .MSIX 包会将其链接到远程应用或桌面。

## <a name="assign-an-msix-package"></a>分配 .MSIX 包 

在 Windows 虚拟桌面中，必须将发布的 .MSIX 包分配到 Active Directory 域服务 (AD DS) 或 Azure Active Directory (Azure AD) 用户或用户组。

## <a name="staging"></a>过渡

过渡涉及两个问题：

- 将 VHD (x) 或 [CIM](#cim) 装载到 VM。
- 通知操作系统 .MSIX 包可用于注册。

## <a name="registration"></a>注册

注册后，可以为用户提供暂存的 .MSIX 包。 注册按用户进行。 如果没有为特定用户显式注册应用程序，则它们将无法运行该应用程序。

有两种类型的注册： "常规" 和 "延迟"。

### <a name="regular-registration"></a>定期注册

在常规注册中，分配给用户的每个应用程序都是完全注册的。 注册在用户登录到会话期间发生，这可能会影响他们开始使用 Windows 虚拟桌面所花费的时间。

### <a name="delayed-registration"></a>延迟注册

在延迟注册中，分配给用户的每个应用程序只是部分注册的。 部分注册是指开始菜单磁贴和双击文件关联已注册。 注册会在用户登录到其会话时进行，因此它对开始使用 Windows 虚拟桌面所需的时间的影响最小。 仅当用户在 .MSIX 包中运行该应用程序时，才完成注册。

延迟注册当前是 .MSIX 应用附加的默认配置。

## <a name="deregistration"></a>注销

取消注册将删除用户的已注册但未运行的 .MSIX 包。 当用户从会话中注销时，就会取消注册。 在注销过程中，.MSIX 应用附加会将特定于用户的应用程序数据推送到本地用户配置文件。

## <a name="destage"></a>转储

Destaging 通知 OS：无法卸载当前未运行且未为任何用户暂存的 .MSIX 包或应用程序。 这会在操作系统中删除对它的所有引用。

## <a name="cim"></a>CIM

.CIM 是 (CimFS) 与复合映像文件系统关联的新文件扩展名。 安装和卸载 CIM 文件会加快 VHD 文件的安装速度。 CIM 还会消耗比 VHD 更少的 CPU 和内存。

下表是 VHD 和 CimFS 之间的性能比较。 在 DSv4 计算机上运行的每个格式中，每个格式的测试运行都为 800 MB。

|  规格                          | VHD                    | CimFS   |
|---------------------------------|--------------------------|-----------|
| 平均装入时间     | 356 ms                     | 255 ms      |
| 平均卸载时间   | 1615 ms                    | 36 ms       |
| 内存占用率 | 6% (8 GB)                       | 2% (8 GB)        |
| CPU (计算高峰)           | 多次极限 | 无影响 |

## <a name="next-steps"></a>后续步骤

若要了解有关 .MSIX 应用附加的详细信息，请参阅 [概述](what-is-app-attach.md) 和 [常见问题解答](app-attach-faq.md)。 否则，开始 [安装应用程序](app-attach.md)。

