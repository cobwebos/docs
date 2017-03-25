---
title: "Azure Active Directory 中的已命名网络 | Microsoft Docs"
description: "配置已命名网络后，可以避免组织拥有的 IP 地址针对“从多个地理区域登录”和“从具有可疑活动的 IP 地址登录”的风险事件生成误报。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 161d36f0bef4b3cd1ac1ad85d0844a3dd8e51e16
ms.lasthandoff: 03/18/2017


---
# <a name="named-networks-in-azure-active-directory"></a>Azure Active Directory 中的已命名网络

> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-known-networks-azure-portal.md)
> * [Azure 经典门户](active-directory-known-networks.md)
>
>


Azure Active Directory 为每个检测到的[风险事件](active-directory-identity-protection-risk-events.md)创建一条记录。 通过 Azure Active Directory 安全报告可以获取风险事件信息，因此可了解环境中用户帐户泄露的可能性。   

对于实际由组织拥有的 IP 地址，Azure Active Directory 可能检测到对“不可能前往异常位置”和“从具有可疑活动的 IP 地址登录”[风险事件类型](active-directory-reporting-risk-events.md#risk-event-types)的误报。 

例如，在以下情况时可能会发生这种问题：

- 在波士顿办公室的用户远程登录到旧金山的数据中心，从而生成了“从多个地理区域登录”风险事件

- 组织中的用户多次以错误的密码尝试登录，从而生成了“从具有可疑活动的 IP 地址登录”风险事件

若要防止在这种情况下生成误导性的风险事件，则应该在组织的公共 IP 地址列表中添加已命名的 IP 地址范围。    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>若要添加组织的公共 IP 地址范围，请执行以下步骤：

1. 登录到 Azure 管理门户。

2. 在左窗格中，单击“Active Directory”。

    ![已知网络](./media/active-directory-known-networks-azure-portal/01.png)

3. 在目录边栏选项卡的“管理”部分，单击“已命名网络”。

    ![已知网络](./media/active-directory-known-networks-azure-portal/02.png)


4. 单击“添加位置”

    ![已知网络](./media/active-directory-known-networks-azure-portal/03.png)

5. 在“添加”边栏选项卡上，执行以下操作：

    ![已知网络](./media/active-directory-known-networks-azure-portal/04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入名称。

    b.保留“数据库类型”设置，即设置为“共享”。 在“IP 范围”文本框中，键入 IP 范围。 IP 范围必须为 CIDR 格式。 对于大量更新，可使用 IP 范围上传 CSV 文件。 上传会将在文件中的 IP 范围添加到列表而不是覆盖列表。

    c. 单击“创建” 。


**其他资源：**

* [查看访问和使用情况报告](active-directory-view-access-usage-reports.md)
* [从具有可疑活动的 IP 地址登录](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [从多个地理区域登录](active-directory-reporting-sign-ins-from-multiple-geographies.md)

