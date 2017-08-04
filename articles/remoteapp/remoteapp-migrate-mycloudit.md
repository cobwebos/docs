---
title: "更改对 Azure RemoteApp 的收费 | Microsoft Docs"
description: "了解如何停止对 Azure RemoteApp 收费。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 8f94da9a-7848-4ddc-b7b7-d9c280ccf4d7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mbaldwin
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 32fc673eeef01e80c73375bf264206beea8cfbe5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="migrate-from-azure-remoteapp-to-mycloudit"></a>从 Azure RemoteApp 迁移到 MyCloudIT 

**目前是否在使用 Microsoft Azure RemoteApp？**：MyCloudIT 构建了一个自动化工具来将 Azure RemoteApp (ARA) 集合迁移到 MyCloudIT 管理平台，在迁移期间继续在 Microsoft Azure 上运行。

**利用 Azure Resource Manager 门户**：完成到 MyCloudIT 平台的迁移后，可以立即访问 Azure 的新 Azure Resource Manager 门户。 此门户包含 Microsoft Azure 提供的所有新功能和创新，包括访问虚拟机大小来确保所构建的部署能够支持你的业务需求。

**并行进行测试以确保获得适合需求的合适解决方案**：MyCloudIT 迁移工具构建为并行启动迁移过程和测试，同时当前 ARA 用户将继续使用 ARA。  用户将保持使用 ARA，直到迁移和测试完成。  构建的迁移工具用来处理典型 ARA 集合。  如果你有独特的非标准方案，请通过 [sales@conexlink.com](mailto:sales@conexlink.com) 与我们联系，以便我们可以提供定制计划来帮助你进行迁移。

**桌面即服务功能**：请注意，MyCloudIT 不仅提供了你习惯使用的 RemoteApp 功能，而且还提供了完整的桌面即服务功能，每月成本相同，且没有任何最少用户数要求。

## <a name="what-we-will-do-for-you"></a>我们将为你执行哪些操作

MyCloudIT 可以使用自动化迁移工具自动将 Azure RemoteApp 模板从订阅的 Azure 经典门户迁移到订阅的 Azure Resource Manager 门户。  

> [!NOTE]
> Azure RemoteApp 模板必须保留在与原始 Azure RemoteApp 部署相同的 Azure 区域中。  如果在迁移期间需要更改 Azure 区域或 Azure 订阅，请通过 [sales@conexlink.com](mailto:sales@conexlink.com) 与我们联系以获得更多指导。

有关通过 MyCloudIT 迁移工具执行的自动化迁移过程的详细信息，请阅读下文：

1. 迁移工具扫描你当前的订阅来查找所有现有的 ARA 部署。  
2. 一次选择要迁移的一个 ARA 集合。  如果有多个集合，请多次运行该工具。
3. 可以选择将用户配置文件磁盘 (UPD) 复制到新部署以便可以检索旧数据，或者手动将 UPD 映射到新部署。 如果选择复制 UPD，则会保存 UPD 并包括一个用于将 UPD 名称映射到每个用户的名称的文本文件。  UPD 将复制到 RDSMGMT 服务器 `F:\Shares\LegacyUPD` 上的一个共享并将通过共享 `\\RDSmgmt\LegacyUPD` 公开。 
4. 进行迁移时，当前 ARA 部署不需要停机。  但是，如果在复制后（从 ARA 中）对 UPD 进行了任何更改，这些更改不会反映在 Azure Resource Manager 门户中存储的 UPD 中。 
5. 如果你的经典 Azure 虚拟网络中有诸如域控制器和文件服务器之类的其他 VM，则会在新的 Azure Resource Manager 门户中在你的现有经典 Azure 虚拟网络与我们为你创建的新虚拟网络之间建立 VNet 对等互连。
6. 只有现有 ARA 部署是混合部署时，我们的自动化解决方案才会在现有经典 Azure 虚拟网络与新虚拟网络之间建立 VNet 对等互连；这意味着，将通过现有经典虚拟网络中的 Windows Server Active Directory 域控制器进行身份验证。 如果没有为你的集合建立 VNet 对等互连，但你需要 VNet 对等互连，请通过 [sales@conexlink.com](mailto:sales@conexlink.com) 与我们联系，我们很乐意免费为你配置 VNet 对等互连。
7. 我们的自动化解决方案将确保使用新的虚拟网络设置更新你的 Azure DNS 配置以确保新部署可以连接到经典 VNet 中的现有域控制器。
8. 我们的自动化解决方案将确保在创建此新虚拟网络时以及为具有现有 Windows Server Active Directory 服务器的部署建立 VNet 对等互连时没有 IP 地址冲突。
9. 如果仅使用 Azure AD 进行身份验证，则 MyCloudIT 将创建一个新的 Windows Server Active Directory 域并使用 Azure AD Connect 在现有 Azure AD 实例与 MyCloudIT 创建的新 Windows Server Active Directory 域之间同步用户。
10. 如果使用 Windows Server Active Directory 域对 ARA 用户进行身份验证，则我们的自动化解决方案会通过 VNet 对等互连将新的 MyCloudIT 部署连接到现有 Windows Server Active Directory 域控制器。
11. 如果使用 Azure Active Directory 域服务进行身份验证，则我们可以为你进行迁移，但请与我们联系以便可以为你创建自定义迁移计划。  请向 [sales@conexlink.com](mailto:sales@conexlink.com) 发送电子邮件。 
12. 在确认要迁移的集合后，坐下来放松一下，我们的自动化解决方案会将 ARA 集合和用户配置文件磁盘（可选）迁移到新的由 MyCloudIT 驱动的远程应用解决方案。
13. 在部署完成后，我们将重新发布与 ARA 中已发布的应用程序相同的应用程序，并且，在部署后，你将能够发布其他应用程序。

## <a name="post-migration-benefits"></a>迁移后优势

1. 我们提供了管理控制台，它可用来管理远程应用部署的整个生命周期。
2. 可以从我们的门户管理虚拟机。  可以根据需要停止 / 启动各个 VM 及调整其大小。
3. 通过管理控制台，可以从我们的管理门户创建和管理用户 / 组。
4. 通过管理控制台，可以与 Office 365 同步用户来创建相同的登录体验。
5. 通过管理控制台，可以创建附加的远程应用和桌面集合，不需要重复支付用户成本，也没有最少用户数要求。 
6. 通过管理控制台，可以发布新的远程应用应用程序。
7. 如果仅在特定时段内需要你的解决方案，可以通过管理控制台来计划远程应用部署的启动和关闭。
8. 通过管理控制台，可以自动安装和配置 Azure 备份代理，该代理提供客户数据的文档保留历史记录。
9. 通过管理控制台，可以访问部署的性能指标。  这使得你能够查明任何潜在的性能瓶颈，而不需要安装额外的性能管理工具。
10. 如果你有多个会话主机，则能够启用自动缩放，以便仅使需要运行的会话主机运行。
11. MyCloudIT 可以通过 MyCloudIT 域名访问 RDWeb 网关服务器。  对于最终用户品牌，还可以将 URL 重新映射到自定义 URL。

## <a name="prerequisites-for-migration"></a>迁移的先决条件

1. 必须能够访问托管着当前 Azure RemoteApp 解决方案的 Azure 订阅。
2. 必须在你的订阅中授权我们的门户迁移你的模板并创建 / 修改你的新 MyCloudIT 部署。
3. 请注意，由于 Azure RemoteApp 中的一个限制，每个集合只能迁移三次。  如果需要将某个集合迁移三次以上，可以向 Azure 提交一个票证来增加你的导出计数，或者与我们联系，我们将通过 ARA 请求帮助你增加导出计数。

## <a name="mycloudit-billing"></a>MyCloudIT 计费

有关如何预测和管理总体 Azure 成本的信息，请参阅 [MyCloudIT Pricing for RemoteApp Solutions](https://mcitdocuments.blob.core.windows.net/terms/MyCloudIT_Pricing_Overview.pdf)（MyCloudIT 针对 RemoteApp 解决方案的定价）(PDF)。

如果仍有疑问，请通过 [sales@conexlink.com](mailto:sales@conexlink.com) 与我们联系或者查看完整的演示视频 [Azure RemoteApp 迁移工具 - MyCloudIT](https://www.youtube.com/watch?v=YQ_1F-JeeLM&t=482s)。 


