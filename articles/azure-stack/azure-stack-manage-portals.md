---
title: 在 Azure Stack 中使用管理员门户 | Microsoft Docs
description: 向 Azure Stack 操作员介绍管理员门户的用法。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: mabrigg
ms.openlocfilehash: 673b1144fe927e0619f5f8638d7e8ce9a181f48c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248514"
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>在 Azure Stack 中使用管理员门户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈; 中有两个门户管理员门户和用户门户 (有时称为*租户*门户。)Azure Stack 操作员可以使用管理员门户进行日常的 Azure Stack 管理和操作。

## <a name="access-the-administrator-portal"></a>访问管理员门户

对于开发工具包环境，首先需确保可以通过远程桌面连接或虚拟专用网络 (VPN) [连接到开发工具包主机](azure-stack-connect-azure-stack.md)。

若要访问管理员门户，请浏览到门户 URL，然后使用 Azure Stack 操作员的凭据登录。 对于集成系统，门户 URL 根据 Azure Stack 部署的区域名称和外部完全限定域名 (FQDN) 而有所不同。

| 环境 | 管理员门户 URL |   
| -- | -- | 
| 开发工具包| https://adminportal.local.azurestack.external  |
| 集成系统 | https://adminportal.&lt;*区域*&gt;。&lt;*FQDN*&gt; | 
| | |

 ![管理员门户](media/azure-stack-manage-portals/image1.png)

在管理员门户中，可以执行如下所述的操作：

* 管理基础结构 （包括系统运行状况、 更新、 容量、 等）。
* 充实 Marketplace
* 为用户创建订阅
* 创建计划和提供

**快速入门教程**磁贴提供指向联机文档的最常见的任务。

尽管运算符具有可以创建资源如虚拟机、 虚拟网络和管理员门户中的存储帐户，你应该[登录到用户门户](user/azure-stack-use-portal.md)来创建和测试资源。

>[!NOTE]
>**创建虚拟机**快速入门教程磁贴中的链接具有你在管理员门户中，创建虚拟机，但这仅用于在首次部署后，验证 Azure 堆栈。

## <a name="understand-subscription-behavior"></a>要了解订阅的行为

没有可用于从管理员门户中使用只有一个订阅。 此订阅是默认提供商订阅。 无法添加任何其他订阅，并在管理员门户中使用它们。

Azure Stack 操作员可在管理员门户中为用户（包括自己）添加订阅。 （包括您自己） 的用户可以访问并使用这些订阅从**用户**门户。 但是，用户门户不提供对任何管理员门户的管理或操作功能的访问。

管理员门户和用户门户基于 Azure 资源管理器的独立实例。 由于此资源管理器分隔，订阅不能跨越门户。 例如，如果你，作为 Azure 堆栈操作员，登录到用户门户，你不能访问*默认提供程序订阅*。 尽管你无权访问任何管理功能，你可以从可用的公共服务来创建自己的订阅。 只要你已登录到用户门户被视为租户用户。

  >[!NOTE]
  >在开发工具包环境中，如果某个用户与 Azure Stack 操作员属于同一个租户目录，则系统不会阻止他们登录到管理员门户。 但是，他们无法访问任何管理功能。 此外，在管理员门户中，它们不能添加订阅或访问提供，可供它们在用户门户中。

## <a name="administrator-portal-tips"></a>管理员门户提示

### <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 你可以选择**编辑仪表板**修改默认的仪表板，或选择**新仪表板**添加自定义仪表板。 你可以轻松添加到仪表板的磁贴。 例如，你可以选择**新建**，右键单击**提供 + 计划**，然后选择**固定到仪表板**。

### <a name="quick-access-to-online-documentation"></a>快速访问联机文档

若要访问 Azure 堆栈运算符文档，使用的帮助和支持中的管理员门户的右上角的图标 （问号）。 将光标移到图标，，然后选择**帮助 + 支持**。

### <a name="quick-access-to-help-and-support"></a>快速访问帮助和支持内容

如果您的管理员门户中，右上角选择 （问号） 中的帮助和支持图标，然后选择**新建支持请求**，发生以下结果之一：

- 如果使用的是集成系统，此操作会打开一个站点，可在其中直接向 Microsoft 客户支持服务 (CSS) 创建支持票证。 请参阅[从何处获得支持](azure-stack-manage-basics.md#where-to-get-support)了解何时应转通过 Microsoft 支持或通过你的原始设备制造商 (OEM) 硬件供应商支持。
- 如果使用的是开发工具包，则此操作会直接打开 Azure Stack 论坛站点。 我们会持续留意这些论坛。 由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中的区域管理](azure-stack-region-management.md)
