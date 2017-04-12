---
title: "Azure RemoteApp 的应用要求 | Microsoft Docs"
description: "了解要在 Azure RemoteApp 中使用的应用的相关要求"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 4427eef6-288a-49e1-97eb-fee67d99f26a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a9a305e4c07e2c348b1c1503d53f1da05da57966
ms.lasthandoff: 03/31/2017


---
# <a name="app-requirements"></a>应用要求
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 支持流式处理 Windows Server 2012 R2 映像中的 32 位或 64 位基于 Windows 的应用程序。 大多数现有的 32 位或 64 位基于 Windows 的应用程序在 Azure RemoteApp（远程桌面服务或以前称为终端服务）环境中按“原样”运行。 但是，运行和运行良好之间存在一定差异 - 一些应用程序功能正常且工作良好，而其他应用程序则相反。 以下信息提供了在远程桌面服务环境中开发应用程序和测试以保证兼容性的指南。

提示：我们正在创建一些应用工作示例，以供参考。 即将推出讨论在 RemoteApp 中使用 Microsoft Access、QuickBooks 和 App-V 的新主题。

## <a name="requirements"></a>要求
遵循以下三个要求有助于应用程序在 RemoteApp 中良好地运行：

1. 符合所有的 [Windows 桌面应用的认证要求](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx)并遵循[远程桌面服务编程指南](https://msdn.microsoft.com/library/aa383490.aspx)的应用程序将与 RemoteApp 完全兼容。
2. 应用程序绝对不能将可能会丢失的数据存储在本地映像或 RemoteApp 实例中。  创建 RemoteApp 集合后，会克隆实例并且这些实例是无状态的，其中应仅包含应用程序。 将数据存储在外部源或存储在用户的配置文件中。
3. 自定义映像绝对不能包含可能会丢失的数据。  

## <a name="testing-your-apps"></a>测试应用
使用以下步骤测试应用程序：

1. 安装 Windows Server 2012 R2 和应用程序
2. 启用远程桌面
3. 创建两个用户帐户，即 UserA 和 UserB，将两个用户帐户全部添加到远程桌面安全组。
4. 在启动应用程序期间，创建连接到电脑的两个同步 RDS 会话，以检查多会话兼容性。
5. 验证应用行为

## <a name="application-development-guidelines"></a>应用程序开发指南
使用以下指南开发适用于 RemoteApp 的应用程序。

### <a name="multiple-users"></a>多用户
* 在多用户环境中安装[用于单个用户的应用程序](https://msdn.microsoft.com/library/aa380661.aspx)可能会产生一些问题。
* 应用程序应在用户特定的位置[存储用户特定的信息](https://msdn.microsoft.com/library/aa383452.aspx)，该位置要独立于应用到所有用户的全局信息区。
* RemoteApp 使用多个[适用于内核对象的命名空间](https://msdn.microsoft.com/library/aa382954.aspx)；全局命名空间主要由客户端/服务器应用程序中的服务使用。
* 分配到计算机的计算机名称或 [IP 地址](https://msdn.microsoft.com/library/aa382942.aspx)与单个用户关联，这种假设并不可靠，因为可以同时将多个用户登录到远程桌面会话主机（RD 会话主机）服务器。

### <a name="performance"></a>性能
* 将应用添加到 RemoteApp 前，请先禁用[图形效果](https://msdn.microsoft.com/library/aa380822.aspx)。
* 若要最大化所有用户的 CPU 可用性，可以禁用[后台任务](https://msdn.microsoft.com/library/aa380665.aspx)或者创建非资源密集型的高效后台任务。
* 应调整和平衡多用户、多处理器环境的应用程序[线程使用情况](https://msdn.microsoft.com/library/aa383520.aspx)。
* [检测](https://msdn.microsoft.com/library/aa380798.aspx)应用程序是否正在客户端会话中运行是优化性能的有效做法。


