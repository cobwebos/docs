---
title: "Azure RemoteApp 最佳实践 | Microsoft Docs"
description: "配置和使用 RemoteApp 的最佳实践。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b851865b-bec4-4f29-82c0-7b9770c1a520
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 9af9f95e9638eda71fbc4508bea8e35ce5191734
ms.openlocfilehash: 3caabe366db4def4f8332c64d08f86f271c31bb2


---
# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>配置和使用 RemoteApp 的最佳实践
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://blogs.technet.microsoft.com/enterprisemobility/2016/08/12/application-remoting-and-the-cloud/) 了解详细信息。
> 
> 

以下信息可帮助你高效地配置和使用 Azure RemoteApp。

## <a name="connectivity"></a>连接
* 始终使用最新的客户端版本。 使用旧版客户端可能会导致连接问题和其他降级体验。 为你的设备启用自动应用程序更新将确保始终安装最新客户端。
* 始终使用对你来说最稳定和最可靠的 Internet 连接。  
* 仅使用受支持的代理连接以获得最佳的连接性能。  不支持 SOCKS 代理。

## <a name="applications"></a>应用程序
* 安装应用程序后保存并关闭 RemoteApp 应用程序。 不关闭该应用程序可能会导致数据丢失。
* 在 Azure RemoteApp 中使用自定义应用程序前需要对其进行验证。 这包括确保其可在多会话平台上工作，且不会占用不必要的资源，例如占用内存和 CPU 可能会使同一集合中的其他用户资源不足。 有关信息，请下载并查看[远程桌面服务的应用程序兼容性最佳实践](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf)。

## <a name="configuration-and-management"></a>配置和管理
* 保持模板图像为最新状态，并根据需要安装软件更新程序和其他关键修补程序。 这可确保 Azure RemoteApp 自动扩展以满足你的容量，且每个实例都得到修补。  
* 确保 Active Directory 联合身份验证服务 (AD FS) 部署安全可靠。 否则，客户端身份验证可能会失败，从而阻止用户访问 Azure RemoteApp。
* 使用安装的应用程序、角色或功能配置模板图像，以使其为无状态。 在处于持久状态的 RemoteApp 服务中，这些模板图像不应依赖于的虚拟机的任何实例。
  * 将所有用户数据存储在用户配置文件或服务外部的其他存储位置，如本地文件共享或 OneDrive。
  * 将共享数据存储在服务外部的存储位置，如本地文件共享或 OneDrive。
  * 在模板映像中配置任何系统范围的设置，而不是在服务中的单个虚拟机上配置。
  * 对已发布的应用程序禁用自动软件更新 - 而是将其手动应用到模板映像，并在从模板进行部署前对其测试。




<!--HONumber=Jan17_HO2-->


