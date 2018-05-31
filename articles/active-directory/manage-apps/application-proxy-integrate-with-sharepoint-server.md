---
title: 通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问 | Microsoft 文档
description: 概要介绍如何将本地 SharePoint 服务器与 Azure AD 应用程序代理相集成。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 49b5620fb95e9c67b0ff0e314534dc8499714b63
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161720"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问

本文介绍如何将本地 SharePoint 服务器与 Azure Active Directory (Azure AD) 应用程序代理相集成。

若要通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问，请一步步完成本文各部分的操作。

## <a name="prerequisites"></a>先决条件

本文假设已在环境中安装 SharePoint 2013 或更高版本。 此外，请注意以下先决条件：

* SharePoint 包含本机 Kerberos 支持。 因此，通过 Azure AD 应用程序代理远程访问内部站点的用户预期可以获得单一登录 (SSO) 体验。

* 此方案包括对 SharePoint 服务器进行配置更改。 建议使用过渡环境。 这样，便可以先对过渡服务器进行更新，在转到生产环境之前简化测试周期。

* 我们要求在已发布的 URL 上使用 SSL。 需要在内部站点上启用 SSL，确保正确发送/映射链接。 如果尚未配置 SSL，请参阅 [Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013)（为 SharePoint 2013 配置 SSL）以获取说明。 此外，请确保连接器计算机信任颁发的证书。 （证书不需要公开颁发。）

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>步骤 1：设置 SharePoint 的单一登录

对于使用 Windows 身份验证的本地应用程序，可以通过 Kerberos 身份验证协议以及一项称为 Kerberos 约束委派 (KCD) 的功能来实现单一登录 SSO。 KCD 在经过配置后，可让应用程序代理连接器获取用户的 Windows 令牌，即使该用户尚未直接登录到 Windows。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](https://technet.microsoft.com/library/jj553400.aspx)。

若要为 SharePoint 服务器设置 KCD，请完成后面按顺序标记的部分中的步骤：

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>确保 SharePoint 在某个服务帐户下运行

首先，请确保 SharePoint 在定义的服务帐户下运行，而不是在本地系统、本地服务或网络服务下运行。 这样做是为了能够将服务主体名称 (SPN) 附加到有效的帐户。 Kerberos 协议使用 SPN 来识别不同的服务。 稍后需要使用该帐户来配置 KCD。

> [!NOTE]
需要为服务预先创建一个 Azure AD 帐户。 建议允许自动密码更改。 有关整套步骤和排查问题的详细信息，请参阅 [Configure automatic password change in SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx)（在 SharePoint 2013 中配置自动密码更改）。

若要确保站点在定义的服务帐户下运行，请执行以下步骤：

1. 打开“SharePoint 2013 管理中心”站点。
2. 转到“安全”并选择“配置服务帐户”。
3. 选择“Web 应用程序池 - SharePoint - 80”。 这些选项可能因 Web 池名称的不同或者 Web 池是否默认使用 SSL 而略有不同。

  ![用于配置服务帐户的选项](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. 如果“为该组件选择帐户”字段设置为“本地服务”或“网络服务”，则需创建一个帐户。 否则，此步骤即告完成，可以转到下一部分。
5. 选择“注册新的托管帐户”。 创建帐户后，必须先设置“Web 应用程序池”，才能使用该帐户。

### <a name="configure-sharepoint-for-kerberos"></a>为 Kerberos 配置 SharePoint

用户使用 KCD 在 SharePoint 服务器上执行单一登录。

若要在 SharePoint 站点中配置 Kerberos 身份验证，请执行以下操作：

1. 打开“SharePoint 2013 管理中心”站点。
2. 转到“应用程序管理”，选择“管理 Web 应用程序”，并选择 SharePoint 站点。 在本示例中，此站点为 **SharePoint - 80**。

  ![选择 SharePoint 站点](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. 在工具栏中单击“身份验证提供程序”。
4. 在“身份验证提供程序”框中，单击“默认区域”查看设置。
5. 在“编辑身份验证”对话框中，向下滚动，直到看到“声明身份验证类型”。 确保同时选中“启用 Windows 身份验证”和“集成 Windows 身份验证”。
6. 在“集成 Windows 身份验证”字段下拉框中，确保选中“协商 (Kerberos)”。

  ![“编辑身份验证”对话框](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. 在“编辑身份验证”对话框的底部，单击“保存”。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>为 SharePoint 服务帐户设置服务主体名称

在配置 KCD 之前，需要识别以配置的服务帐户运行的 SharePoint 服务。 通过设置 SPN 标识服务。 有关详细信息，请参阅 [Service Principal Names](https://technet.microsoft.com/library/cc961723.aspx)（服务主体名称）。

SPN 格式为：

```
<service class>/<host>:<port>
```

在 SPN 格式中：

* _service class_ 是服务的唯一名称。 对于 SharePoint，请使用 **HTTP**。

* _host_ 是运行服务的主机的完全限定域名或 NetBIOS 名称。 对于 SharePoint 站点，此文本可能必须是站点的 URL，具体取决于使用的 IIS 版本。

* _port_ 是可选的。

如果 SharePoint 服务器的 FQDN 为：

```
sharepoint.demo.o365identity.us
```

则 SPN 为：

```
HTTP/sharepoint.demo.o365identity.us demo
```

可能还需要为服务器上的特定站点设置 SPN。 有关详细信息，请参阅 [Configure Kerberos authentication](https://technet.microsoft.com/library/cc263449(v=office.12).aspx)（配置 Kerberos 身份验证）。 请重点查看“使用 Kerberos 身份验证为 Web 应用程序创建服务主体名称”部分。

设置 SPN 的最简单方法是遵循站点现在可能已设置了的 SPN 格式。 复制这些 SPN，以便对服务帐户注册。 为此，请按以下步骤操作：

1. 从另一台计算机使用 SPN 浏览到该站点。
 执行此操作时，相关的 Kerberos 票证集会在计算机上缓存。 这些票证包含浏览到的目标站点的 SPN。

2. 可以使用一个名为 [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html) 的工具来拉取该站点的 SPN。 在特定的命令行窗口中运行以下命令，该窗口运行时所依据的上下文与用户通过浏览器访问站点时依据的上下文相同：
```
Klist
```
然后，Klist 会返回目标 SPN 的集合。 在本示例中，突出显示的值是所需的 SPN：

  ![Klist 结果示例](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. 设置 SPN 后，请确保在前面针对 Web 应用程序设置的服务帐户中正确配置该 SPN。 请在命令提示符下以域管理员的身份运行以下命令：

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 此命令针对以 _demo\sp_svc_ 运行的 SharePoint 服务帐户设置 SPN。

 请将 _http/sharepoint.demo.o365identity.us_ 替换为服务器的 SPN，将 _demo\sp_svc_ 替换为环境中的服务帐户。 Setspn 命令会先搜索 SPN，再添加它。 在这种情况下，可能会出现“SPN 值重复”错误。 如果看到此错误，请确保该值与服务帐户关联。

可以结合 -l 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要详细了解该命令，请参阅 [Setspn](https://technet.microsoft.com/library/cc731241.aspx)。

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>确保连接器已设置为 SharePoint 的受信任委派

配置 KCD，使 Azure AD 应用程序代理服务能够向 SharePoint 服务委派用户标识。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序（在本例中为 SharePoint）。

若要配置 KCD，请针对每个连接器计算机重复以下步骤：

1. 以域管理员的身份登录到 DC，并打开“Active Directory 用户和计算机”。
2. 找到运行连接器的计算机。 在本示例中，它是同一台 SharePoint 服务器。
3. 双击该计算机，并单击“委派”选项卡。
4. 确保委派设置指定为“仅信任此计算机来委派指定的服务”。 然后，选择“使用任意身份验证协议”。

  ![委派设置](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. 单击“添加”按钮，然后单击“用户或计算机”并找到服务帐户。

  ![添加服务帐户的 SPN](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. 在 SPN 列表中，选择此前为服务帐户创建的 SPN。
7. 单击“确定”。 再次单击“确定”保存更改。

## <a name="step-2-enable-remote-access-to-sharepoint"></a>步骤 2：启用对 SharePoint 的远程访问

由于已启用了适用于 Kerberos 的 SharePoint 并配置了 KCD，现在便可通过 Azure AD 应用程序代理发布 SharePoint 场以进行远程访问。

1. 使用以下设置发布 SharePoint 网站。 有关分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-publish-azure-portal.md)。 
   - 内部 URL：SharePoint 站点内部使用的 URL，如 **https://SharePoint/**。 在本示例中，请务必使用 https
   - 预身份验证方法：Azure Active Directory
   - 转换标头中的 URL：否

   >[!TIP]
   >SharePoint 使用_主机标头_ 值来查找站点， 并根据该值生成链接。 这样做的实质影响是 SharePoint 生成的任何链接是已正确设置为使用外部 URL 的已发布 URL。 将此值设置为“是”还能让连接器将请求转发到后端应用程序。 但是，将此值设置为“否”意味着连接器不会向后端应用程序发送内部主机名， 而是发送主机标头充当已发布 URL。

   ![发布 SharePoint 应用程序](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. 发布应用后，请执行以下步骤来配置单一登录设置：

   1. 在门户中的应用程序页上，选择“单一登录”。
   2. 对于单一登录模式，选择“集成 Windows 身份验证”。
   3. 将“内部应用程序 SPN”设置为此前设置的值。 此示例中为 http/sharepoint.demo.o365identity.us。

   ![为 SSO 配置集成 Windows 身份验证](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. 要完成应用程序设置，请转到“用户和组”部分，分配要访问此应用程序的用户。 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>步骤 3：确保 SharePoint 识别外部 URL

最后一个步骤是确保 SharePoint 能够根据外部 URL 找到站点，以便根据该外部 URL 呈现链接。 为此，请为 SharePoint 站点配置备用访问映射。

1. 打开“SharePoint 2013 管理中心”站点。
2. 在“系统设置”下，选择“配置备用访问映射”。 将打开“备用访问映射”框。

  ![“备用访问映射”框](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. 在“备用访问映射集合”旁边的下拉列表中，选中“更改备用访问映射集合”。
4. 选择站点，例如 **SharePoint - 80**。
5. 可以选择将已发布的 URL 添加为内部 URL 或公共 URL。 本示例使用一个公共 URL 作为 Extranet。
6. 在“Extranet”路径中单击“编辑公共 URL”，然后输入发布应用程序时创建的外部 URL。 例如，输入 https://sharepoint-iddemo.msappproxy.net。

  ![输入路径](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. 单击“ **保存**”。

现在，可以通过 Azure AD 应用程序代理从外部访问 SharePoint 站点。

## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)
- [了解 Azure AD 应用程序代理连接器](application-proxy-connectors.md)

