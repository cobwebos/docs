---
title: 虚拟机测试驱动器 Azure Marketplace 的云合作伙伴门户中的选项卡
description: 介绍用于创建 Azure 市场 VM 套餐的“体验版”选项卡。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 31c7968d0d96a44ff166444f73807e0ccb5dc583
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938001"
---
# <a name="virtual-machine-test-drive-tab"></a>虚拟机“体验版”选项卡

在“新建套餐”页的“体验版”选项卡中，可以通过标准化的方案，以动手实践、自我引导的方式向潜在客户演示产品的关键功能和优势。  体验版是支持体验版的套餐类型的一项可选功能。  体验版需要支持性的资产才能正常实现。  有关详细信息，请参阅 [Azure 市场体验版](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)一文。  <!--TD: Replace with migrated version of Test Drive article! -->

若要启用此功能，请在“体验版”选项卡中，单击“启用体验版”对应的选项“是”。  “体验版”选项卡会显示可编辑的字段。  名称旁边附有星号 (*) 的字段表示必填字段。

![虚拟机“新建套餐”窗体中的“体验版”选项卡](./media/publishvm_007.png)


## <a name="field-values"></a>字段值

下表描述了这些字段的用途和内容。  必填字段用星号 (*) 表示。


|    字段                  |       描述                                                            |
|  ---------                |     ---------------                                                          |
|  *详细信息*   |  |
| **说明\***           | 提供体验版方案的概述。 预配体验版时，会向用户显示此文本。 若要提供带格式的内容，可在此字段中输入基本 HTML 内容。  |
| **用户手册\***           | 上传详细的用户手册 (.pdf)，以帮助体验版用户了解如何使用你的解决方案。  |
| **体验版演示视频** | 上传用于展示解决方案的视频。  如果选择此选项，必须提供视频的名称和 URL（托管在 YouTube 或 Vimeo 中），以及视频的缩略图（533x324 像素）。 |
| *技术配置* |  |
| **实例\***             | 指定 VM 实例的可用区域和相对可用性（单击信息图标可查看更多详细信息）。  <br/>潜在的并发体验版会话数不应超过订阅的配额限制。  前者的计算公式为：[选定区域数] x [热实例数] + [选定区域数] x [暖实例数] + [选定区域数] x [冷实例数] |
| **测试版持续时间\***   | 最大会话持续时间，以小时为单位。 超过此时间段后，体验版会话将自动终止。  |
|**测试驱动器 ARM 模板\***| 上传与此体验版关联的 Azure 资源管理器模板。 有关详细信息，请参阅[转换体验版的虚拟机部署模板](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)。 |
| **访问信息\***    | Azure 资源管理器的访问和试用登录信息，以纯文本或简单 HTML 的格式编写。 |
| *体验版部署订阅详细信息* |  |
| **Azure 订阅 Id\*** | 可通过以下方式获取此 ID：登录到 [Microsoft Azure 门户](https://ms.portal.azure.com)，然后单击左侧菜单栏中的“订阅”。 （示例：“a83645ac-1234-5ab6-6789-1h234g764ghty”）。此标识符应是 `a83645ac-1234-5ab6-6789-1h234g764ghty` 格式的 GUID。|
| **Azure AD 租户 Id\***    | Azure Active Directory 租户 ID。  可通过以下方式获取此 ID：登录到 [Microsoft Azure 门户](https://ms.portal.azure.com)，在左侧菜单栏中单击“Azure Active Directory”，在中间菜单栏中单击“属性”，然后复制窗体中的“目录 ID”。  此标识符应是一个 GUID。  如果为空，则必须为组织创建一个租户 ID。 |
| **Azure AD App Id\***       | 已注册的 Azure VM 解决方案的标识符  |
| **Azure AD 应用密钥\***      | 已注册的解决方案的身份验证密钥 |
|   |   |


## <a name="next-steps"></a>后续步骤

在后面的 [市场](./cpp-marketplace-tab.md) 选项卡中，提供有关解决方案的营销和法律信息。
