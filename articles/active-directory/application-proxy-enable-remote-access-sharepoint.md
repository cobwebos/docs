---
title: "使用 Azure AD 应用代理启用对 SharePoint 的远程访问 | Microsoft 文档"
description: "介绍有关 Azure AD 应用程序代理连接器的基础知识。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>使用 Azure AD 应用代理启用对 SharePoint 的远程访问

本文介绍如何将本地 SharePoint 服务器集成到 Azure AD 应用程序代理。

> [!NOTE]
> 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。
> 

##<a name="prerequisites"></a>先决条件

本文假设已在环境中安装并运行 SharePoint 2013 或更高版本。 此外，请注意以下先决条件：

* SharePoint 包含本机 Kerberos 支持。 因此，通过 Azure AD 应用程序代理远程访问内部站点的用户预期可以获得顺畅的单一登录体验。

* 需要对 SharePoint 服务器进行少量的配置更改。 建议使用过渡环境。 这样，便可以先对过渡服务器进行更新，然后，在转到生产环境之前简化测试周期。

* 假设已经为 SharePoint 设置了 SSL，因为需要对发布的 URL 使用 SSL。 需要在内部站点上启用 SSL，确保正确发送/映射链接。 如果尚未配置 SSL，请参阅 [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013)（为 SharePoint 2013 配置 SSL）中有关设置 SSL 的说明。 此外，请确保连接器计算机信任颁发的证书。 （不需要是公开颁发的证书。）

##<a name="steps-to-set-up-sharepoint"></a>设置 SharePoint 的步骤

遵循以下步骤，使用 Azure AD 应用代理启用对 SharePoint 的远程访问：

**第 1 部分：配置单一登录 (SSO)**

  * 步骤 A. 确保 SharePoint 服务器以服务帐户的形式运行。
  * 步骤 B. 为 Kerberos 配置 SharePoint。
  * 步骤 C. 为分配给 SharePoint 的帐户设置服务主体名称 (SPN)。
  * 步骤 D. 确保连接器已设置为 SharePoint 的受信任委派。

**第 2 部分：启用安全远程访问**

 * 将 SharePoint 场发布到 Azure AD 应用代理。

**第 3 部分：确保 SharePoint 了解外部 URL**

 * 在 SharePoint 中设置备用访问映射。

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>第 1 部分：设置 SharePoint 的单一登录 (SSO)

我们的客户希望能够在其后端应用程序（在本例中为 SharePoint 服务器）中获得最佳的 SSO 体验。 在这种常见的 Azure AD 方案中，用户只需执行身份验证一次，因为系统不会再次提示身份验证。

对于需要或者使用 Windows 身份验证的本地应用程序，可以使用 Kerberos 身份验证协议以及一个称作 Kerberos 约束委派 (KCD) 的功能来实现此目的。 KCD 在经过配置后，可让应用程序代理连接器获取给定用户的 Windows 票证/令牌，即使该用户尚未直接登录到 Windows。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](https://technet.microsoft.com/en-us/library/jj553400.aspx)。

遵循以下步骤为 SharePoint 服务器完成此项设置。

**步骤 A：确保 SharePoint 在某个服务帐户下运行，而不是在本地系统、本地服务或网络服务下运行**

要做的第一件事是确保 SharePoint 在定义的服务帐户下运行。 这样做的目的是为了能够将服务主体名称 (SPN) 附加到有效的帐户。 Kerberos 协议使用 SPN 来识别不同的服务。 并且，稍后需要使用 SPN 来配置 KCD。

若要确保站点在定义的服务帐户下运行，请执行以下操作：

1. 打开“SharePoint 2013 管理中心”站点。
2. 转到“安全”并选择“配置服务帐户”。
3. 选择“Web 应用程序池 - SharePoint - 80”。 根据 Web 池的名称，或者在默认使用 SSL 的情况下，这些选项可能略有不同。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. 如果“为该组件选择帐户”为“本地服务”或“网络服务”，则需要创建一个帐户。 否则，此步骤即告完成，可以转到下一步。 
5. 选择“注册新的托管帐户”。 创建帐户后，必须先设置“Web 应用程序池”，然后才能使用该帐户。

> [!NOTE]
需要为服务预先创建一个 AD 帐户。 建议允许自动密码更改。 有关整套步骤和排查问题的详细信息，请参阅 [Configure automatic password change in SharePoint 2013](https://technet.microsoft.com/EN-US/library/ff724280.aspx)（在 SharePoint 2013 中配置自动密码更改）。 

**步骤 B：为 Kerberos 配置 SharePoint**

我们使用 KCD 在 SharePoint 服务器上执行单一登录 (SSO)，而这只能配合 Kerberos 来实现。 

若要在 SharePoint 站点中配置 Kerberos 身份验证，请执行以下操作：

1. 打开“SharePoint 2013 管理中心”站点。
2. 转到“应用程序管理”，选择“管理 Web 应用程序”，然后选择你的 SharePoint 站点。 在本示例中，此站点为 **SharePoint – 80**。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. 在工具栏中单击“身份验证提供程序”。
4. 在“身份验证提供程序”框中，单击“默认区域”查看设置。
5. 在“编辑身份验证”框中向下滚动，直到出现“声明身份验证类型”，确保“启用 Windows 身份验证”和“Windows 集成身份验证”均已选中。 
6. 在下拉框中，确保“协商(Kerberos)”已选中。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. 在“编辑身份验证”框的最底部，单击“保存”。

**步骤 C：设置 SharePoint 服务帐户的 SPN**

在配置 KCD 之前，需要识别以先前配置的服务帐户运行的 SharePoint 服务。 为此，可以设置服务主体名称 (SPN)。 有关详细信息，请参阅 [Service Principal Names](https://technet.microsoft.com/en-us/library/cc961723.aspx)（服务主体名称）。

SPN 格式为：

```
<service class>/<host>:<port>
```

_service class_ 是服务的唯一名称。 对于 SharePoint，我们将使用 HTTP。

_host_ 是运行服务的主机的完全限定域名或 Netbios 名称。 对于 SharePoint 站点，这可能需要是站点的 URL，具体取决于使用的 IIS 版本。

_port_ 是可选的。 如果 SharePoint 服务器的 FQDN 为：

```
sharepoint.demo.o365identity.us
```

则 SPN 为： 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

除此之外，可能还需要为服务器上的特定站点设置 SPN。 有关详细信息，请参阅 [Configure Kerberos authentication](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx)（配置 Kerberos 身份验证）。 请务必密切关注“使用 Kerberos 身份验证为 Web 应用程序创建服务主体名称”部分。 

完成此配置的最简单方法就是遵循站点现有的 SPN 格式。 复制这些 SPN，以便对服务帐户注册。 为此，请按以下步骤操作：

1. 从另一台计算机使用 SPN 浏览到该站点。 
 执行此操作时，相关的 Kerberos 票证集将在计算机上缓存。 这些票证包含浏览到的目标站点的 SPN。 可以使用一个名为 [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html) 的工具来提取该站点的 SPN。
 
2. 在通过浏览器访问该站点的用户所在的同一上下文中运行的命令窗口中，运行以下命令： 
```
Klist
```
3. 此命令将返回目标服务的 SPN 集。 在本示例中，突出显示的值是所需的 SPN：

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. 设置 SPN 后，需确保在前面针对 Web 应用程序设置的服务帐户中正确配置该 SPN。 请遵循下一部分中的步骤。

**设置 SPN**

若要设置 SPN，请在命令提示符下以域管理员的身份运行以下命令。

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

此命令针对以 _demo\sp_svc_ 运行的 SharePoint 服务帐户设置 SPN。

请记得将 _http/sharepoint.demo.o365identity.us_ 替换为服务器的 SPN，将 _demo\sp_svc_ 替换为环境中的服务帐户。 setspn 命令会先搜索 SPN，然后再添加它。 在这种情况下，可能会出现“重复的 SPN 值”错误。 如果看到此错误，请确保该值与服务帐户关联。

可以结合 -l 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要了解有关 Setspn 工具的详细信息，请参阅 [Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx)。

**步骤 D：确保连接器是 SharePoint 的受信任委派**

此步骤将配置 KCD，使 Azure AD 应用代理服务能够向 SharePoint 服务委派用户标识。 为此，可让应用代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。 

若要配置 KCD，需要针对每个连接器计算机重复以下步骤：

1. 以域管理员的身份登录到 DC，然后打开“Active Directory 用户和计算机”。
2. 找到运行连接器的计算机。 在本示例中，它是同一台 SharePoint 服务器。
3. 双击该计算机，然后单击“委派”选项卡。
4. 确保委派设置指定为“仅信任此计算机来委派指定的服务”，然后选择“使用任何身份验证协议”。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. 现在，需要为服务帐户添加前面所创建的 SPN。 单击“添加”按钮，然后单击“用户或计算机”，找到前面创建的帐户。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 此时应会出现 SPN 列表，可从中进行选择。 需要添加前面设置的 SPN。 
6. 选择该项，然后单击“确定”。 再次单击“确定”保存更改。

### <a name="part-2-enable-remote-access-to-sharepoint"></a>第 2 部分：启用对 SharePoint 的远程访问

在 SharePoint 中启用 Kerberos 并配置的 KCD 后，接下来可以设置 SharePoint 的单一登录 (SSO)。 然后，可以使用连接器通过 Azure AD 应用程序代理发布 SharePoint 进行远程访问。

**使用 Azure AD 应用代理发布 SharePoint**

若要执行以下步骤，你需是组织的 Azure Active Directory 帐户中全局管理员角色的成员。

1. 登录到 Azure 管理门户 (https://manage.windowsazure.com) 并找到你的 Azure AD 租户。
2. 单击“应用程序”，然后单击“添加”。
3. 选择“ **发布可从你的网络外部访问的应用程序**”。 如果未看到此选项，请确保已在租户中设置了 Azure AD Basic 或 Premium。
4. 在出现的框中，按如下所示完成每个选项： 
 * **名称**：任何所需值，例如 _SharePoint_。
 * **内部 URL**：SharePoint 站点内部使用的 URL，例如 https://SharePoint/。 在本示例中，请务必使用 https。
 * **预身份验证方法**：选择“Azure Active Directory”。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. 发布应用后，单击“配置”选项卡。
6. 向下滚动到“转换标头中的 URL”选项。 默认值为“是”，请更改为“否”。 

 SharePoint 使用“主机标头”值来查找站点，并基于此值生成链接。 这样做的实质影响是可以确保 SharePoint 生成的任何链接是已正确设置为使用外部 URL 的已发布 URL。 将此值设置为“是”还能让连接器将请求转发到后端应用程序。 但是，将此值设置为“否”则意味着连接器不会发送内部主机名，而是将主机标头作为已发布的 URL 发送到后端应用程序。

 此外，为了确保 SharePoint 接受此 URL，还需要在 SharePoint 服务器上完成一项配置。 将在下一部分完成此操作。

7. 将“内部身份验证方法”更改为“Windows 集成”。 如果 Azure AD 租户在云中与在本地使用的 UPN 不同，请记得同时更新“委派的登录标识”。
8. 将“内部应用程序 SPN”设置为前面设置的值。 例如 _http/sharepoint.demo.o365identity.us_。
9. 现在，可将应用程序分配给目标用户。

应用程序应如下图所示：

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>第 3 部分：确保 SharePoint 了解外部 URL

需要执行的最后一个步骤是确保 SharePoint 能够基于外部 URL 找到站点，以便基于该外部 URL 呈现链接。 为此，可为 SharePoint 配置备用访问映射。

**配置 SharePoint 站点的备用名称**

1. 打开“SharePoint 2013 管理中心”站点。
2. 在“系统设置”下，选择“配置备用访问映射”。 

 此时将打开“备用访问映射”框。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. 在下拉列表中的“备用访问映射集合”旁边，选中“更改备用访问映射集合”。
4. 选择站点，例如 **SharePoint - 80**。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. 可以选择将已发布的 URL 添加为内部 URL 或公共 URL。 本示例使用“公共 URL”为 Extranet。
6. 在“Extranet”路径中单击“编辑公共 URL”，然后输入应用程序的发布路径，如上一步骤中所示。 例如 _https://sharepoint-iddemo.msappproxy.net_。

  ![AzureAD 应用程序代理连接器](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. 单击“保存” 。 

 现在，可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

##<a name="next-steps"></a>后续步骤

[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)<br>
[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)<br>
[使用 Azure AD 应用程序代理发布 SharePoint 2016 和 Office Online Server](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)









<!--HONumber=Feb17_HO1-->


