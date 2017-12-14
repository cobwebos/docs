---
title: "Azure Active Directory 域服务：入门 | Microsoft Docs"
description: "使用 Azure 门户启用 Azure Active Directory 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 16b206b97f71eeffbb4fddb9e447649034998672
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 门户启用 Azure Active Directory 域服务
本文演示如何使用 Azure 门户启用 Azure Active Directory 域服务 (Azure AD DS)。

若要启动“启用 Azure AD 域服务”向导，请完成以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在左窗格中，单击“新建”。
3. 在“新建”页中，在搜索栏中输入“域服务”。

    ![搜索域服务](./media/getting-started/search-domain-services.png)

4. 单击以从搜索建议列表中选择“Azure AD 域服务”。 在“Azure AD 域服务”页上，单击“创建”按钮。

    ![“域服务”视图](./media/getting-started/domain-services-blade.png)

5. “启用 Azure AD 域服务”向导随即启动。


## <a name="task-1-configure-basic-settings"></a>任务 1：配置基本设置
在向导的“基本信息”页中，可以为托管域指定 DNS 域名。 还可以选择应向其中部署托管域的资源组和 Azure 位置。

![配置基本信息](./media/getting-started/domain-services-blade-basics.png)

1. 为托管域选择“DNS 域名”。

   > [!NOTE]
   > **选择 DNS 域名的准则**
   > * **内置域名：**默认情况下，该向导指定目录的默认/内置域名（带 **.onmicrosoft.com** 后缀）。 如果选择启用通过 Internet 对托管域进行安全 LDAP 访问，则创建公共 DNS 记录或从此域名的公共 CA 获取安全 LDAP 证书时会遇到问题。 Microsoft 拥有 *.onmicrosoft.com* 域，CA 不会为此域颁发证书担保。
   * **自定义域名：**也可以键入自定义域名。 在此示例中，自定义域名为 *contoso100.com*。
   * **不可路由的域后缀：**通常建议尽量避免使用不可路由的域名后缀。 例如，最好避免创建 DNS 域名为“contoso.local”的域。 “.local”DNS 后缀不可路由，并可能导致 DNS 解析出现问题。
   * **域前缀限制：**指定域名的前缀（例如 *contoso100.com* 域名中的 *contoso100*）所包含的字符不得超过 15 个。 不能创建前缀长度超过 15 个字符的托管域。
   * **网络名称冲突：**确保为托管域选择的 DNS 域名尚未在虚拟网络中存在。 具体而言，检查是否有以下情况：
       * 虚拟网络中已存在具有相同 DNS 域名的 Active Directory 域。
       * 计划在其中启用托管域的虚拟网络具有与本地网络之间的 VPN 连接。 在此方案中，确保在本地网络上没有具有相同 DNS 域名的域。
       * 虚拟网络上存在具有该名称的云服务。
    >

2. 选择要在其中创建托管域的 Azure“订阅”。

3. 选择托管域应属于的“资源组”。 可以选择“新建”或“使用现有”选项以选择资源组。

4. 选择应在其中创建托管域的 Azure“位置”。 在向导的“网络”页上，只会看到属于所选位置的虚拟网络。

5. 完成后，单击“确定”以前进到向导的“网络”页。


## <a name="next-step"></a>后续步骤
[任务 2：配置网络设置](active-directory-ds-getting-started-network.md)
