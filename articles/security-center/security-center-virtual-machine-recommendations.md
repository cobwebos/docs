---
title: "保护 Azure 安全中心中的虚拟机 | Microsoft 文档"
description: "本文档介绍 Azure 安全中心中的建议，以帮助你保护虚拟机并保持符合安全策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 9f08f73eb4ce8fc46b4f4ba5c2d1fd22319537d2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>保护 Azure 安全中心中的虚拟机
Azure 安全中心可分析 Azure 资源的安全状态。 在安全中心识别潜在的安全漏洞时，它会创建一些建议，这些建议会指导你完成配置所需控件的过程。  适用于 Azure 资源类型的建议：虚拟机 (VM)、网络、SQL，以及应用程序。

本文介绍适用于 VM 的建议。  数据收集、应用系统更新、设置反恶意软件、加密 VM 磁盘等的 VM 建议中心。  使用下表作为参考来帮助了解可用的 VM 建议，以及应用建议后，每个建议的做法。

## <a name="available-vm-recommendations"></a>可用的 VM 建议
| 建议 | 说明 |
| --- | --- |
| [为订阅启用数据收集](security-center-enable-data-collection.md) |建议为每个订阅和订阅中的所有虚拟机 (VM) 开启安全策略中的数据收集。 |
| [为 Azure 存储帐户启用加密](security-center-enable-encryption-for-storage-account.md) | 建议为静态数据启用 Azure 存储服务加密。 存储服务加密 (SSE) 的工作原理是，在将数据写入 Azure 存储时对数据进行加密，以及在检索前对数据进行解密。 SSE 当前仅适用于 Azure Blob 服务，并可用于块 blob、页 blob 和追加 blob。 若要了解详细信息，请参阅[静态数据的存储服务加密](../storage/storage-service-encryption.md)。</br>只有 Resource Manager 存储帐户支持 SSE。 目前不支持经典存储帐户。 若要了解经典部署模型和 Resource Manager 部署模型，请参阅 [Azure 部署模型](../azure-classic-rm.md)。 |
| [修正 OS 漏洞](security-center-remediate-os-vulnerabilities.md) |建议 OS 配置匹配推荐的配置规则，例如不允许保存密码。 |
| [应用系统更新](security-center-apply-system-updates.md) |建议在 VM 上部署缺少的系统安全和重要更新。 |
| [应用实时网络访问控制](security-center-just-in-time.md) | 建议应用实时 VM 访问。 实时功能处于预览状态，并在安全中心的标准层上可用。 请参阅[定价](security-center-pricing.md)，详细了解安全中心的定价层。 |
| [在系统更新后重启](security-center-apply-system-updates.md#reboot-after-system-updates) |建议重启 VM 以完成应用系统更新的过程。 |
| [安装终结点保护](security-center-install-endpoint-protection.md) |建议在 VM（仅 Windows VM）上预配反恶意程序。 |
| [解决终结点保护运行状况警报](security-center-resolve-endpoint-protection-health-alerts.md) |建议解决终结点保护故障。 |
| [启用 VM 代理](security-center-enable-vm-agent.md) |使你能够查看需要 VM 代理的 VM。 若要设置修补程序扫描、基线扫描以及反恶意程序，则必须在 VM 上安装 VM 代理。 对于从 Azure 应用商店部署的 VM，默认安装 VM 代理。 文章 [VM 代理和扩展 - 第 2 部分](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)提供有关如何安装 VM 代理的信息。 |
| [应用磁盘加密](security-center-apply-disk-encryption.md) |建议使用 Azure 磁盘加密（Windows 和 Linux VM）对 VM 磁盘进行加密。 对于 VM 上的 OS 和数据卷，建议使用加密。 |
| [更新 OS 版本](security-center-update-os-version.md) |建议将云服务的操作系统 (OS) 版本更新为 OS 系列可用的最新版本。  若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。 |
| [未安装漏洞评估](security-center-vulnerability-assessment-recommendations.md) |建议在 VM 上安装漏洞评估解决方案。 |
| [修正漏洞](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |使你可以查看由安装在 VM 上的漏洞评估解决方案上检测到的系统和应用程序漏洞。 |

## <a name="see-also"></a>另请参阅
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

* [保护 Azure 安全中心中的应用程序](security-center-application-recommendations.md)
* [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
* [保护 Azure 安全中心中的 Azure SQL 服务](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md) - 了解如何配置 Azure 订阅和资源组的安全策略。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [Azure 安全中心常见问题](security-center-faq.md) - 查找有关使用服务的常见问题。

