---
title: "在 Azure 安全中心中安装终结点保护 | Microsoft Docs"
description: "本文档演示如何实现 Azure 安全中心建议**安装终结点保护**。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b8a69d89f335c00c5ddc3c201e33a66e1dea1da5
ms.openlocfilehash: 6ccb5367b1c7c6cae7b9d35b6a5471c8edfe6f85


---
# <a name="install-endpoint-protection-in-azure-security-center"></a>在 Azure 安全中心中安装终结点保护
如果尚未启用反恶意软件，Azure 安全中心将建议将反恶意软件程序设置到 Azure 虚拟机 (VM)。 此建议仅适用于 Windows VM。 目前，此建议检查是否存在 Windows Defender 或 TrendMicro Deep Security。 计划在将来添加其他终结点保护解决方案。

> [!NOTE]
> 本文档通过使用示例部署介绍该服务。  这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“安装终结点保护”。
   ![选择安装终结点保护][1]
2. “安装终结点保护”边栏选项卡将打开，显示未启用反恶意软件的 VM 列表。 从列表中选择想要在其上安装反恶意软件的 VM，单击“在 VM 上安装”。
   ![选择要在其上安装反恶意软件的 VM][2]
3. “选择终结点保护”边栏选项卡将打开，以允许你选择想要使用的反恶意软件解决方案。 在此示例中，请选择“Microsoft 反恶意软件”。
   ![选择终结点保护][3]
4. 将显示有关反恶意软件解决方案的其他信息。 选择“创建” 。
   ![创建反恶意软件解决方案][4]
5. 在“添加扩展”边栏选项卡输入所需的配置设置，然后选择“确定”。 若要了解有关配置设置的详细信息，请参阅[默认和自定义反恶意软件配置](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration)。

[Microsoft 反恶意软件](../security/azure-security-antimalware.md)目前在所选 VM 上处于活动状态。

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议“安装终结点保护。” 若要了解有关在 Azure 中启用反恶意软件程序的详细信息，请参阅以下内容：

* [适用于云服务和虚拟机的 Microsoft 反恶意软件](../security/azure-security-antimalware.md) - 了解如何部署 Microsoft 反恶意软件。

若要了解有关安全中心的详细信息，请参阅以下文章：

* [Setting security policies in Azure Security Center](security-center-policies.md)（在 Azure 安全中心设置安全策略）- 了解如何配置安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) - 了解建议如何帮助你保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png



<!--HONumber=Nov16_HO4-->


