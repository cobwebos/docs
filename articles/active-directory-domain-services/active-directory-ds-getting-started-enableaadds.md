---
title: "Azure AD 域服务：启用 Azure AD 域服务 | Microsoft Docs"
description: "Azure Active Directory 域服务入门"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 64bb5f7e78a6e48faf3487da780597b25891a2fa
ms.lasthandoff: 12/08/2016


---
# <a name="enable-azure-ad-domain-services"></a>启用 Azure AD 域服务
## <a name="task-3-enable-azure-ad-domain-services"></a>任务 3：启用 Azure AD 域服务
在此任务中，为目录启用 Azure AD 域服务。 执行以下配置步骤，为目录启用 Azure AD 域服务。

1. 导航到 **Azure 经典门户** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 在左窗格中，选择“Active Directory”节点。
3. 选择要启用 Azure AD 域服务的 Azure AD 租户（目录）。

    ![选择 Azure AD 目录](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 单击“配置”  选项卡。

    ![目录的“配置”选项卡](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. 向下滚动到标题为“域服务”的部分。

    ![域服务配置部分](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)
6. 将标题为“为此目录启用域服务”的选项切换为“是”。 可以看到，页面上出现了 Azure AD 域服务的更多一些配置选项。

    ![启用域服务](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > 为租户启用 Azure AD 域服务时，Azure AD 将生成并存储对用户进行身份验证时所需的 Kerberos 和 NTLM 凭据哈希。
   >
   >
7. 指定“域服务的 DNS 域名”。

   * 默认情况下，将选择目录的默认域名（即，末尾为 **.onmicrosoft.com** 域后缀的域名）。
   * 列表包含已针对 Azure AD 目录配置的所有域，包括在“域”选项卡中配置的已验证和未验证域。
   * 此外，也可以键入自定义域名。 在本示例中，键入了自定义域名“contoso100.com”。

     > [!WARNING]
     > 确保指定的域名的域前缀（例如“contoso100.com”域名中的“contoso100”）少于 15 个字符。 不能创建域前缀超过 15 个字符的 Azure AD 域服务域。
     >
     >
8. 确保虚拟网络中不存在为托管域选择的 DNS 域名。 具体而言，检查是否有以下情况：

   * 虚拟网络中已存在具有相同 DNS 域名的域。
   * 选择的虚拟网络已通过 VPN 连接到本地网络，而本地网络上存在具有相同 DNS 域名的域。
   * 虚拟网络上存在具有该名称的云服务。
9. 下一步是选择要在其中使用 Azure AD 域服务的虚拟网络。 在标题为“将域服务连接到此虚拟网络”的下拉列表中，选择已创建的虚拟网络和专用子网。

   * 确保指定的虚拟网络属于 Azure AD 域服务支持的 Azure 区域。 请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)页，了解 Azure AD 域服务可用的 Azure 区域。
   * 属于不支持 Azure AD 域服务的区域的虚拟网络不会出现在此下拉列表中。
   * 对 Azure AD 域服务使用虚拟网络中的专用子网。 确保未选择网关子网。 请参阅[网络注意事项](active-directory-ds-networking.md)。
   * 同样，使用 Azure Resource Manager 创建的虚拟网络也不会出现在此下拉列表中。 Azure AD 域服务目前不支持基于 Resource Manager 的虚拟网络。
10. 若要启用 Azure AD 域服务，请单击页面底部任务窗格中的“保存”。
11. 为目录启用 Azure AD 域服务时， 该页面将显示“正在挂起...”状态。

    ![启用域服务 - 等待状态](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [!NOTE]
    > Azure AD 域服务为托管域提供高可用性。 启用 Azure AD 域服务后，可以看到，可在虚拟网络上使用的域服务的 IP 地址会逐个显示。 服务为域启用高可用性后，第二个 IP 地址将立即显示。 为域配置高可用性并将其激活后，应会在“配置”选项卡的“域服务”部分中看到两个 IP 地址。
    >
    >
12. 大约 20-30 分钟后，将在“配置”页的“IP 地址”字段中看到可在虚拟网络上使用的域服务的第一个 IP 地址。

    ![域服务已启用 - 已预配第一个 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
13. 如果域的高可用性正常运行，将在页面上看到两个 IP 地址。 托管域已在这两个 IP 地址对应的所选虚拟网络上可用。 请记下这些 IP 地址，以便更新虚拟网络的 DNS 设置。 此步骤可使虚拟网络上的虚拟机连接到域，执行域加入等操作。

    ![域服务已启用 - 已预配两个 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> 同步到托管域需要一段时间，具体取决于 Azure AD 租户的大小（用户、组等的数量）。 此同步过程在后台发生。 对于包含几万个对象的大型租户，可能需要一天或两天的时间，才能同步所有用户、组成员资格和凭据。
>
>

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>任务 4 - 更新 Azure 虚拟网络的 DNS 设置
下一个配置任务是 [更新 Azure 虚拟网络的 DNS 设置](active-directory-ds-getting-started-dns.md)。

