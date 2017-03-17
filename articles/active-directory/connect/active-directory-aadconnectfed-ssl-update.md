---
title: "Azure AD Connect：更新 Active Directory 联合身份验证服务 (AD FS) 场的 SSL 证书 | Microsoft 文档"
description: "本文档详述了使用 Azure AD Connect 更新 AD FS 场的 SSL 证书的步骤。"
services: active-directory
keywords: "azure ad connect, adfs ssl 更新, adfs 证书更新, 更改 adfs 证书, 新建 adfs 证书, adfs 证书, 更新 adfs ssl 证书, 更新 ssl 证书 adfs, 配置 adfs ssl 证书, adfs, ssl, 证书, adfs 服务通信证书, 更新联合身份验证, 配置联合身份验证, aad connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>更新 Active Directory 联合身份验证服务 (AD FS) 场的 SSL 证书

## <a name="overview"></a>概述
本文详述了如何使用 Azure AD Connect 更新 Active Directory 联合身份验证服务 (AD FS) 场的 SSL 证书。  如果将 Azure AD Connect 中的登录方法设置为 AD FS，则可使用 Azure AD Connect 工具跨所有联合服务器和 WAP 服务器轻松地更新 AD FS 场的 SSL 证书，只需简单的 3 步：

![三个步骤](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>若要详细了解 AD FS 所使用的证书，请参阅[了解 AD FS 所使用的证书](https://technet.microsoft.com/library/cc730660.aspx)一文

##<a name="pre-requisites"></a>先决条件

* **AD FS 场**：AD FS 场应该是 2012R2 或更高版本
* **Azure AD Connect**：确保 Azure AD Connect 为 1.1.443.0 或更高版本。 可以找到任务“更新 AD FS SSL 证书”

![更新 SSL 任务](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>步骤 1：提供 AD FS 场信息

AAD Connect 会尝试通过以下方式自动获取有关 AD FS 场的信息：
1. 从 AD FS（2016 或更高版本）查询场信息
2. 参考之前运行的信息（通过 Azure AD Connect 存储在本地）。 

可以根据 AD FS 场的当前配置添加或删除服务器，以便修改所显示服务器的列表。 只要提供了服务器信息，Azure AD Connect 就会显示连接情况和当前的 SSL 证书状态。

![AD FS 服务器信息](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

如果列表中包含的服务器不再是 AD FS 场的一部分，则可单击“删除”将服务器从 AD FS 场中服务器的列表中删除。 

![列表中的脱机服务器](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> 从 Azure AD Connect 的 AD FS 场的服务器列表中进行删除属于本地操作，更新的是 Azure AD Connect 保留在本地的 AD FS 场的信息。 Azure AD Connect 不会根据所做的更改对 AD FS 上的配置进行任何修改。    

##<a name="step-2-provide-new-ssl-certificate"></a>步骤 2：提供新的 SSL 证书

确认有关 AD FS 场服务器的信息以后，Azure AD Connect 会要求用户提供新的 SSL 证书。 请提供受密码保护的 PFX 证书，继续进行安装。 

![SSL 证书](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
提供证书以后，Azure AD Connect 会对证书进行一系列的先决条件检查，确保证书适用于 AD FS 场：
1.    证书的使用者名称 (SN)/使用者替代名称 (SAN) 与联合身份验证服务名称相同，或者证书是通配符证书。
2.    证书的有效期超过 30 天。
3.    证书信任链有效 
4.    证书受密码保护

##<a name="step-3-select-servers-for-update"></a>步骤 3：选择要更新的服务器

请在下一步选择其上的 SSL 证书需要更新的服务器。 不能选择脱机的服务器进行更新。 

![选择要更新的服务器](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

配置完成后，Azure AD Connect 会显示一条指示更新状态的消息，并会提供一个验证 AD FS 登录的选项。

![配置完成](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>常见问题

* **对于新的 AD FS SSL 证书，证书的使用者名称应该是什么？**

    Azure AD Connect 会检查证书的使用者名称/使用者替代名称是否包含联合身份验证服务名称。 例如，如果联合身份验证服务名称为 fs.contoso.com，则使用者名称/使用者替代名称必须为 fs.contoso.com。  也接受通配符证书。 

* **为什么在 WAP 服务器页上又要求我提供凭据？**

    如果连接到 AD FS 服务器时提供的凭据没有同时管理 WAP 服务器的特权，则 Azure AD Connect 会要求用户提供在 WAP 服务器上具有管理特权的凭据。

* **服务器显示为脱机，我该怎么办？**

    如果服务器处于脱机状态，Azure AD Connect 将无法执行任何操作。 如果服务器是 AD FS 场的一部分，请检查到服务器的连接，在解决问题后按刷新图标，以便更新向导中的状态。 如果服务器此前是场的一部分，但现在不再存在，则请单击“删除”，将其从 Azure AD Connect 保留的服务器列表中删除。  从 Azure AD Connect 的列表中删除服务器不会更改 AD FS 配置本身。如果用户使用的是 AD FS 2016 或更高版本，则服务器会保留在配置设置中，下次运行任务时会再次显示。

* **能否使用新的 SSL 证书更新一部分场服务器？**

    是的。 你始终可以再次运行“更新 SSL 证书”任务，对剩下的服务器进行更新。 在“选择进行 SSL 证书更新的服务器”页上，用户可以根据“SSL 到期日期”对服务器列表排序，轻松访问尚未更新的服务器。 

* **我在上次运行时删除了服务器，但该服务器仍显示为脱机并且在“AD FS 服务器”页中列出。为什么该脱机服务器在删除后仍显示在那儿？**

    从 Azure AD Connect 的列表中删除服务器并不会将其从 AD FS 配置中删除。 Azure AD Connect 也会参考 AD FS（2016 或更高版本）来获取有关场的任何信息。如果服务器仍然存在于 AD FS 配置中，则会重新在列表中列出。  

## <a name="next-steps"></a>后续步骤

[Azure AD Connect 和联合身份验证](active-directory-aadconnectfed-whatis.md)
[使用 Azure AD Connect 进行 Active Directory 联合身份验证服务的管理和自定义](active-directory-aadconnect-federation-management.md)

