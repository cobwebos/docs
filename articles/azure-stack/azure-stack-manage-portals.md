---
title: "使用 Azure 堆栈中的管理员门户 |Microsoft 文档"
description: "作为 Azure 堆栈操作员，了解如何使用管理员门户。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>在 Azure 堆栈中使用管理员门户

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈; 中有两个门户管理员门户和用户门户 (有时称为*租户*门户)。 作为 Azure 堆栈操作员，可以使用管理员门户，用于日常管理和 Azure 堆栈的操作。 

## <a name="access-the-administrator-portal"></a>访问管理员门户

对于开发工具包环境中，你需要首先确保你可以[连接到开发工具包主机](azure-stack-connect-azure-stack.md)通过远程桌面连接或通过虚拟专用网络 (VPN)。

若要访问管理员门户中，浏览到门户的 URL 并通过使用 Azure 堆栈运算符的凭据登录。 对于集成系统，URL 而异的门户基于区域名称和外部完全限定的域名 (FQDN) 的 Azure 堆栈部署。

| 环境 | 管理员门户 URL |   
| -- | -- | 
| 开发工具包| https://adminportal.local.azurestack.external  |
| 集成系统 | https://adminportal。&lt;*区域*&gt;。&lt;*FQDN*&gt; | 
| | |

 ![管理员门户](media/azure-stack-manage-portals/image1.png)

在管理员门户中，你可以执行以下事项：

* 管理基础结构 （包括系统运行状况、 更新、 容量、 等）。
* 填充应用商店
* 创建计划和提供
* 为用户创建订阅

在**快速入门教程**磁贴中，有链接指向联机文档的最常见的任务。
 
尽管没有一个运算符，以在管理员门户中创建虚拟机、 虚拟网络和存储帐户等资源的能力，但你应[登录到用户门户](user/azure-stack-use-portal.md)来创建和测试资源。 (**创建虚拟机**快速入门教程磁贴中的链接具有你在管理员门户中，创建虚拟机，但此过程是仅在初始部署之后验证 Azure 堆栈。)

## <a name="subscription-behavior"></a>订阅行为
 
没有管理员门户中提供的只有一个订阅。 此订阅是*默认提供程序订阅*。 无法在管理员门户来添加任何其他订阅使用。

作为 Azure 堆栈操作员，你可以将订阅添加用户 （包括您自己） 在管理员门户中。 （包括您自己） 的用户可以访问，并使用这些订阅从用户门户。 用户门户不提供对任何管理员门户的管理或操作功能的访问。

由单独的实例的 Azure 资源管理器中支持的管理员和用户门户。 由于资源管理器分离，订阅不能跨越门户。 例如，如果你为 Azure 堆栈运算符登录到用户门户时，你无法访问默认提供程序订阅。 因此，你无权访问任何管理功能。 你可以从公共优惠，创建自己的订阅，但被视为租户用户。

  >[!NOTE]
  在开发工具包环境中，如果用户属于相同的租户目录与 Azure 堆栈运算符，它们将不阻止在登录到管理员门户。 但是，他们无法访问任何管理功能。 此外，在管理员门户中，它们不能添加订阅或访问提供程序都提供给它们在用户门户中。

## <a name="administrator-portal-tips"></a>管理员门户提示

### <a name="customize-the-dashboard"></a>自定义仪表板

仪表板包含一组默认磁贴。 你可以单击**编辑仪表板**修改默认的仪表板，或单击**新仪表板**添加自定义仪表板。 你可以轻松添加到仪表板的磁贴。 例如，你可以单击**新建**，右键单击**提供 + 计划**，然后单击**固定到仪表板**。

### <a name="quick-access-to-online-documentation"></a>快速访问联机文档

若要访问 Azure 堆栈运算符文档，在管理员门户中，右上角中单击 （问号） 中的帮助和支持图标，然后单击**帮助 + 支持**。

### <a name="quick-access-to-help-and-support"></a>快速访问帮助和支持

如果你的管理员门户中，右上角单击 （问号） 中的帮助和支持图标，然后单击**新建支持请求**，这将执行下列操作之一：

- 如果你使用的集成的系统，此操作将打开在其中你可以直接打开支持票证与 Microsoft 客户支持服务 (CSS) 的站点。 请参阅"从何处获得支持"部分的[Azure 堆栈管理基础知识](azure-stack-manage-basics.md)了解何时应转通过 Microsoft 支持或通过你的原始设备制造商 (OEM) 硬件供应商支持。
- 如果你使用的开发工具包，此操作将直接打开 Azure 堆栈论坛站点。 定期监视这些论坛。 由于开发工具包是评估环境，没有通过 Microsoft CSS 提供正式支持。

## <a name="next-steps"></a>后续步骤

- [Azure 堆栈中的区域管理](azure-stack-region-management.md)
