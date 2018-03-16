---
title: "在 Azure Stack 中使用管理员门户 | Microsoft Docs"
description: "向 Azure Stack 操作员介绍管理员门户的用法。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 34d9d207225327758d535a11c870019f3db31cb0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2018
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>在 Azure Stack 中使用管理员门户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 中有两种门户：管理员门户和用户门户（有时称作租户门户）。 Azure Stack 操作员可以使用管理员门户进行日常的 Azure Stack 管理和操作。 

## <a name="access-the-administrator-portal"></a>访问管理员门户

对于开发工具包环境，首先需确保可以通过远程桌面连接或虚拟专用网络 (VPN) [连接到开发工具包主机](azure-stack-connect-azure-stack.md)。

若要访问管理员门户，请浏览到门户 URL，然后使用 Azure Stack 操作员的凭据登录。 对于集成系统，门户 URL 根据 Azure Stack 部署的区域名称和外部完全限定域名 (FQDN) 而有所不同。

| 环境 | 管理员门户 URL |   
| -- | -- | 
| 开发工具包| https://adminportal.local.azurestack.external  |
| 集成系统 | https://adminportal.&lt;*区域*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![管理员门户](media/azure-stack-manage-portals/image1.png)

在管理员门户中，可以执行如下所述的操作：

* 管理基础结构（包括系统运行状况、更新、容量等）
* 填充 Marketplace
* 创建计划和产品
* 为用户创建订阅

“快速入门教程”磁贴中提供了最常见任务的联机文档链接。
 
尽管操作员可在管理员门户中创建虚拟机、虚拟网络和存储帐户等资源，但应该[登录到用户门户](user/azure-stack-use-portal.md)来创建和测试资源。 （使用“快速入门教程”磁贴中的“创建虚拟机”链接可在管理员门户中创建虚拟机，但此过程只是用于在完成初始部署后验证 Azure Stack）。

## <a name="subscription-behavior"></a>订阅行为
 
管理员门户中只提供一个订阅。 此订阅是默认的提供程序订阅。 无法添加可在管理员门户中使用的其他任何订阅。

Azure Stack 操作员可在管理员门户中为用户（包括自己）添加订阅。 用户（包括自己）可以通过用户门户访问并使用这些订阅。 在用户门户中无法访问管理员门户的任何管理或操作功能。

管理员门户和用户门户基于 Azure 资源管理器的独立实例。 由于资源管理器的分隔性，订阅不会跨门户。 例如，如果以 Azure Stack 操作员的身份登录到用户门户，则无法访问默认的提供程序订阅。 因此，无法访问任何管理功能。 可从公共产品为自己创建订阅，但系统仍会将你视为租户用户。

  >[!NOTE]
  在开发工具包环境中，如果某个用户与 Azure Stack 操作员属于同一个租户目录，则系统不会阻止他们登录到管理员门户。 但是，他们无法访问任何管理功能。 此外，他们无法通过管理员门户添加订阅，或者在用户门户中访问可供他们使用的产品。

## <a name="administrator-portal-tips"></a>管理员门户提示

### <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 可以单击“编辑仪表板”来修改默认仪表板，或者单击“新建仪表板”来添加自定义仪表板。 可以轻松地将磁贴添加到仪表板中。 例如，可以单击“新建”，右键单击“产品 + 计划”，然后单击“固定到仪表板”。

### <a name="quick-access-to-online-documentation"></a>快速访问联机文档

若要访问 Azure Stack 操作员文档，请单击管理员门户右上角的“帮助和支持”图标（问号），然后单击“帮助 + 支持”。

### <a name="quick-access-to-help-and-support"></a>快速访问帮助和支持内容

如果单击管理员门户右上角的“帮助和支持”图标（问号），然后单击“新建支持请求”，则会发生以下情况之一：

- 如果使用的是集成系统，此操作会打开一个站点，可在其中直接向 Microsoft 客户支持服务 (CSS) 创建支持票证。 若要了解何时应该获取 Microsoft 支持或原始设备制造商 (OEM) 硬件供应商支持，请参阅 [Azure Stack 管理员基础知识](azure-stack-manage-basics.md)中的“在何处获取支持”部分。
- 如果使用的是开发工具包，则此操作会直接打开 Azure Stack 论坛站点。 我们会持续留意这些论坛。 由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中的区域管理](azure-stack-region-management.md)
