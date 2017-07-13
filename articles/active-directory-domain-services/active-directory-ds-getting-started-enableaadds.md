---
title: "Azure Active Directory 域服务：启用 Azure Active Directory 域服务 | Microsoft Docs"
description: "通过 Azure 经典门户启用 Azure Active Directory 域服务"
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
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: ed72325ca9db99405c6173eb882a92f80cd77f47
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017


---
# 通过 Azure 经典门户启用 Azure Active Directory 域服务
<a id="enable-azure-active-directory-domain-services-using-the-azure-classic-portal" class="xliff"></a>

## 任务 3：启用 Azure Active Directory 域服务
<a id="task-3-enable-azure-active-directory-domain-services" class="xliff"></a>
在此任务中，请执行以下步骤，为目录启用 Azure Active Directory 域服务 (Azure AD DS)：

1. 转到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左窗格中，选择“Active Directory”按钮。
3. 选择要启用 Azure AD DS 的 Azure Active Directory (Azure AD) 租户（目录）。

    ![选择 Azure AD 目录](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 在“预览目录”页上，单击“配置”选项卡。

    ![目录的“配置”选项卡](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. 在“域服务”下，将“对该目录启用域服务”选项更改为“是”。  
    此时会在页面上显示其他 Azure Active Directory 域服务配置选项。

    ![启用域服务](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > 为租户启用 Azure Active Directory 域服务时，Azure AD 将生成并存储对用户进行身份验证时所需的 Kerberos 和 NTLM 凭据哈希。
   >
   >
6. 指定“域服务的 DNS 域名”。

   * 默认情况下，将选择目录的默认域名（带 **.onmicrosoft.com** 后缀）。

   * 列表包含已针对 Azure AD 目录配置的所有域，包括在“域”选项卡中配置的已验证和未验证域。

   * 也可输入自定义域名。 在此示例中，自定义域名为 *contoso100.com*。

     > [!WARNING]
     > 指定域名的前缀（例如 *contoso100.com* 域名中的 *contoso100*）所包含的字符不得超过 15 个。 不能创建其前缀包含的字符串超过 15 个的 Azure Active Directory 域服务。
     >
     >
7. 确保虚拟网络中不存在为托管域选择的 DNS 域名。 具体而言，请查看是否符合以下要求：

   * 虚拟网络中已存在具有相同 DNS 域名的域。

   * 选择的虚拟网络已通过 VPN 连接到本地网络，而本地网络上存在具有相同 DNS 域名的域。

   * 虚拟网络上存在具有该名称的云服务。
8. 选择需要在其上提供 Azure Active Directory 域服务的虚拟网络。 在“将域服务连接到此虚拟网络”下拉列表中，选择已创建的虚拟网络和专用子网。 也请考虑以下要求：

   * 确保指定的虚拟网络属于 Azure Active Directory 域服务支持的 Azure 区域。 若要确定提供 Azure Active Directory 域服务的 Azure 区域，请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)。

   * 属于不支持 Azure Active Directory 域服务的区域的虚拟网络不会出现在此下拉列表中。

   * 对 Azure Active Directory 域服务使用虚拟网络中的专用子网。 请*勿*选择网关子网。 请参阅[网络注意事项](active-directory-ds-networking.md)。

   * 同样，使用 Azure Resource Manager 创建的虚拟网络也不会出现在此下拉列表中。 Azure Active Directory 域服务目前不支持基于 Resource Manager 的虚拟网络。
9. 若要启用 Azure Active Directory 域服务，请单击页面底部任务窗格中的“保存”。
    * 虽然已为目录启用 Azure Active Directory 域服务，但页面仍显示“挂起”状态。

        ![“启用域服务”窗口](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory 域服务为托管域提供高可用性。 启用 Azure Active Directory 域服务后，可在虚拟网络上使用的域服务的 IP 地址会逐个显示。 服务为域启用高可用性后，第二个 IP 地址将在第一个 IP 地址显示后很快显示。 为域配置高可用性并将其激活后，应会在“配置”选项卡的“域服务”部分中看到两个 IP 地址。
        >
        >
    * 大约 20 到 30 分钟后，将在“配置”页的“IP 地址”字段中显示可在虚拟网络上使用的域服务的第一个 IP 地址。

        ![“域服务”窗口显示首次预配的 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * 如果域的高可用性正常运行，将在页面上显示两个 IP 地址。 托管域已在这两个 IP 地址对应的所选虚拟网络上可用。

10. 请记下这两个 IP 地址，以便更新虚拟网络的 DNS 设置。 这样做可使虚拟网络上的虚拟机连接到域，执行域加入等操作。

    ![“域服务”窗口，显示两个已预配的 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> 同步到托管域需要一段时间，具体取决于 Azure AD 租户的大小（例如，用户或组的数量）。 此同步过程在后台发生。 对于包含几万个对象的大型租户，可能需要一天或两天的时间，才能同步所有用户、组成员资格和凭据。
>
>

## 后续步骤
<a id="next-step" class="xliff"></a>
[任务 4：更新 Azure 虚拟网络的 DNS 设置](active-directory-ds-getting-started-update-dns.md)

