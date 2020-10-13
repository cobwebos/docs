---
title: FortiGate 部署指南 | Microsoft Docs
description: 设置并使用 Fortinet FortiGate 下一代防火墙产品。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273324"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 部署指南

使用本部署指南，你可以了解如何设置和使用 Fortinet FortiGate 下一代防火墙产品。

## <a name="redeem-the-fortigate-license"></a>兑换 FortiGate 许可证

Fortinet FortiGate 下一代防火墙产品在 Azure 基础结构即服务 (IaaS) 中作为虚拟机提供。 此虚拟机有两种许可模式：即用即付和自带许可证。

Fortinet 可以为 Azure Active Directory (Azure AD) 的“投入生产型安全混合访问 (SHA)”团队的成员提供许可证。 即使尚未提供任何许可证，即用即付部署也会正常运行。

如果已颁发许可证，则 Fortinet 会提供一个必须联机兑换的注册代码。

![FortiGate SSL VPN 注册代码的屏幕截图。](registration-code.png)

1. 在 https://support.fortinet.com/ 注册。
2. 注册后，在 https://support.fortinet.com/ 登录。
3. 转到“资产” > “注册/激活”。
4. 输入 Fortinet 提供的注册代码。
5. 指定注册代码，选择“该产品将由非政府用户使用”，然后选择“下一步”。 
6. 输入产品说明（例如 FortiGate），将 Fortinet 合作伙伴设置为“其他” > “Microsoft”，然后选择“下一步”。  
7. 接受“Fortinet 产品注册协议”并选择“下一步”。 
8. 接受“条款”，然后选择“确认”。 
9. 选择“许可证文件下载”，并保存许可证备用。


## <a name="download-firmware"></a>下载固件

Fortinet FortiGate Azure VM 当前未提供进行 SAML 身份验证所需的固件版本。 必须从 Fortinet 获取最新版本。

1. 在 https://support.fortinet.com/ 上登录。
2. 转到“下载” > “固件映像”。
3. 在“发行说明”的右侧，选择“下载”。 
4. 选择 **v6.** > **6.** > **6.4.** 。
5. 选择同一行上的 HTTPS 链接，下载“FGT_VM64_AZURE-v6-build1637-FORTINET.out”。 
6. 保存该文件备用。


## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 转到 https://portal.azure.com ，登录到要在其中部署 FortiGate 虚拟机的订阅。
2. 创建一个新的资源组，或打开要在其中部署 FortiGate 虚拟机的资源组。
3. 选择“添加”。
4. 在“在市场中搜索”中，输入“Forti”。 选择“Fortinet FortiGate 下一代防火墙”。
5. 选择软件计划（如果你有许可证，则选择“自带许可”；如果没有，则选择“即用即付”）。 选择“创建”。
6. 填充 VM 配置。

    ![“创建虚拟机”的屏幕截图。](virtual-machine.png)

7. 将“身份验证类型”设置为“密码”，并提供 VM 的管理凭据。 
8. 选择“查看 + 创建” > “创建”。
9. 等待 VM 部署完成。


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>设置静态公共 IP 地址并分配完全限定的域名

为获得一致的用户体验，请将分配给 FortiGate VM 的公共 IP 地址设置为静态分配。 此外，请将其映射到完全限定的域名 (FQDN)。

1. 转到 https://portal.azure.com 并打开 FortiGate VM 的设置。
2. 在“概览”屏幕上，选择该公共 IP 地址。

    ![FortiGate SSL VPN 的屏幕截图。](public-ip-address.png)

3. 选择“静态” > “保存”。

如果你拥有要在其中部署 FortiGate VM 的环境的公共可路由域名，请为 VM 创建一个主机 (A) 记录。 此记录映射到上一个静态分配的公共 IP 地址。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>为 TCP 端口创建新的入站网络安全组规则

1. 转到 https://portal.azure.com 并打开 FortiGate VM 的设置。
2. 在左侧菜单中选择“网络”。 这将列出网络接口并显示入站端口规则。
3. 选择“添加入站端口规则”。
4. 为 TCP 8443 创建新的入站端口规则。

    ![“添加入站安全规则”的屏幕截图。](port-rule.png)

5. 选择“添加”。


## <a name="create-a-custom-azure-app-for-fortigate"></a>为 FortiGate 创建自定义 Azure 应用

1. 转到 https://portal.azure.com ，为租户打开将提供 FortiGate 登录标识的 Azure AD 窗格。
2. 在左侧菜单中，选择“企业应用程序”。
3. 选择“新建应用程序” > “非库应用程序”。 
4. 输入一个名称（例如 FortiGate），然后选择“添加”。
5. 在左侧菜单中，选择“用户和组”****。
6. 添加能够登录的用户，然后选择“分配”。
7. 在左侧菜单中，选择“单一登录”。
8. 选择“SAML”。
9. 在“基本 SAML 配置”下，选择铅笔图标以编辑配置。
10. 进行下列配置：
    - “标识符(实体 ID)”为 `https://<address>/remote/saml/metadata`。
    - “回复 URL (断言使用者服务 URL)”为 `https://<address>/remote/saml/login`。
    - “注销 URL”为 `https://<address>/remote/saml/logout`。

    `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

11. 记录以下每个 URL 备用：实体 ID、回复 URL 和注销 URL。
12. 选择“保存”，并关闭“基本 SAML 配置”。
13. 在“3 – SAML 签名证书”下，下载“证书(Base64)”并将其保存备用。 
14. 在“4 – 设置(应用名称)”下，复制 Azure 登录 URL、Azure AD 标识符以及 Azure 注销 URL，并将它们保存备用。
15. 在“2 – 用户属性和声明”下，选择铅笔图标以编辑配置。
16. 选择“添加新声明”，并将名称设置为“username”。
17. 将“源属性”设置为“user.userprincipalname”。
18. 选择“保存” > “添加组声明” > “所有组”。  
19. 选择“自定义组声明的名称”，并将名称设置为“group”。
20. 选择“保存”  。


## <a name="prepare-for-group-matching"></a>为组匹配做准备

登录后，FortiGate 允许基于组成员身份提供不同的用户门户体验。 例如，可能会针对营销组提供一种体验，而针对财务组提供另一种体验。 下面介绍了如何为用户创建组：

1. 转到 https://portal.azure.com ，为租户打开将提供 FortiGate 登录标识的 Azure AD 窗格。
2. 选择“组” > “新建组”。 
3. 创建具有以下详细信息的组：
    - 组类型 = 安全
    - 组名 = `a meaningful name`
    - 组说明 = `a meaningful description for the group`
    - 成员身份类型 = 已分配
    - 成员 = `users for the user experience that will map to this group`
4. 对于任何其他用户体验，请重复步骤 3 和 4。
5. 创建组后，选择每个组并记录其对象 ID。
6. 保存这些对象 ID 和组名备用。


## <a name="configure-the-fortigate-vm"></a>配置 FortiGate VM

以下各部分将引导你完成设置 FortiGate VM 的过程。

### <a name="install-the-license"></a>安装许可证

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 如果部署使用“自带许可证”模型，你会看到要求上传许可证的提示。 选择之前创建的许可证文件，将其上传。 选择“确定”，重启 FortiGate VM。

    ![FortiGate VM 许可证的屏幕截图。](license.png)

5. 重启后，使用管理员凭据再次登录以验证许可证。

### <a name="update-firmware"></a>更新固件

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “固件”。 
5. 在“固件管理”中选择“浏览”，然后选择之前下载的固件文件。 
6. 忽略警告，选择“备份配置并升级”。

    ![“固件管理”的屏幕截图。](backup-configure-upgrade.png)

7. 选择“继续”。 
8. 当系统提示保存 FortiGate 配置（以 .conf 文件的形式）时，请选择“保存”。
9. 等待固件上传并应用。 等待 FortiGate VM 重启。
10. 在 FortiGate VM 重启后，使用管理员凭据再次登录。
11. 当系统提示你设置仪表板时，选择“稍后”。
12. 教程视频开始播放时，选择“确定”。

### <a name="change-the-management-port-to-tcp"></a>将管理端口更改为 TCP

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统”。
5. 在“管理设置”下，将 HTTPS 端口更改为“8443”，然后选择“应用”。  
6. 应用更改后，浏览器会尝试重载管理页面，但会失败。 从现在开始，管理页面地址是 `https://<address>`。

    ![“上传远程证书”的屏幕截图。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>上传 Azure AD SAML 签名证书

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “证书”。 
5. 选择“导入” > “远程证书” 。
6. 浏览到从 Azure 租户中的 FortiGate 自定义应用部署下载的证书。 将其选中，然后选择“确定”。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上传和配置自定义 SSL 证书

你可能希望使用自己的 SSL 证书（该证书支持你使用的 FQDN）配置 FortiGate VM。 如果你有权访问以 PFX 格式与私钥打包在一起的 SSL 证书，则该证书可用于此目的。

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 继续操作，忽略任何证书错误。
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
4. 在左侧菜单中，选择“系统” > “证书”。 
5. 选择“导入” > “本地证书” > “PKCS #12 证书”。  
6. 浏览到包含 SSL 证书和私钥的 .PFX 文件。
7. 为证书提供 .PFX 密码和一个有意义的名称。 然后选择“确定”。 
8. 在左侧菜单中，选择“系统” > “设置”。 
9. 在“管理设置”下，展开“HTTPS 服务器证书”旁的列表，选择前面导入的 SSL 证书。 
10. 选择“应用”。
11. 关闭浏览器窗口并转到 `https://<address>`。
12. 使用 FortiGate 管理员凭据登录。 你现在应会看到正在使用正确的 SSL 证书。


### <a name="perform-command-line-configuration"></a>执行命令行配置

以下各部分提供了使用命令行进行各种配置的步骤。

#### <a name="for-saml-authentication"></a>对于 SAML 身份验证

1. 转到 https://portal.azure.com 并打开 FortiGate VM 的设置。
2. 在左侧菜单中，选择“串行控制台”。
3. 在串行控制台中使用 FortiGate VM 管理员凭据登录。 在下一步中，需要提供前面记录的 URL：
    - 实体 ID
    - 回复 URL
    - 注销 URL
    - Azure 登录 URL
    - Azure AD 标识符
    - Azure 注销 URL
4. 在串行控制台中运行以下命令：

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure 注销 URL 包含 `?` 字符。 这需要一个特殊的键序列才能将其正确提供给 FortiGate 串行控制台。 该 URL 通常为 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`。 若要在串行控制台中提供此项，请键入：
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    然后，选择 Ctrl+V 并粘贴 URL 的其余部分以完成该行： 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. 若要确认配置，请运行以下命令：

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>对于组匹配

1. 转到 https://portal.azure.com 并打开 FortiGate VM 的设置。
2. 在左侧菜单中，选择“串行控制台”。
3. 在串行控制台中使用 FortiGate VM 管理员凭据登录。
4. 在串行控制台中运行以下命令：

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    对于在 FortiGate 中将会有不同的门户体验的每个附加组，请重复这些命令（从第二行代码开始）。

#### <a name="for-authentication-timeout"></a>对于身份验证超时

1. 转到 https://portal.azure.com 并打开 FortiGate VM 的设置。
2. 在左侧菜单中，选择“串行控制台”。
3. 在串行控制台中使用 FortiGate VM 管理员凭据登录。
4. 在串行控制台中运行以下命令：

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>创建 VPN 门户和防火墙策略

1. 转到 `https://<address>`。 这里的 `<address>` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址。

2. 使用在 FortiGate VM 部署期间提供的管理员凭据登录。
3. 在左侧菜单中，选择“VPN” > “SSL-VPN 门户” > “新建”。  
6. 提供一个名称（通常与用于提供自定义门户体验的 Azure 组相匹配）。
7. 依次选择“源 IP 池”旁边的加号 ( **+** )、默认池、“关闭”。 
8. 自定义此组的体验。 对于测试，这可以是对门户消息和主题的自定义。 你也可在此处创建将用户定向到内部资源的自定义书签。
9. 选择“确定”。
10. 对于将具有自定义门户体验的每个 Azure 组，请重复步骤 5 到 9。
11. 在“VPN”下，选择“SSL-VPN 设置”。
12. 依次选择“侦听接口”旁边的加号 ( **+** )、“Port1”、“关闭”。
14. 如果你之前安装了自定义 SSL 证书，请在下拉菜单中更改“服务器证书”以使用自定义 SSL 证书。
15. 在“身份验证/门户映射”下，选择“新建” 。 选择第一个 Azure 组，并将其与相同名称的门户匹配。 然后选择“确定”。 
18. 对 Azure 组和门户的每个配对重复步骤 15-17。
19. 在“身份验证/门户映射”下，编辑“所有其他用户/组” 。
20. 将门户设置为“完全访问权限”，然后选择“确定” > “应用”。 
23. 滚动到“SSL-VPN 设置”页的顶部，选择“不存在 SSL-VPN 策略。单击此处以使用这些设置创建新的 SSL-VPN 策略”。
24. 提供一个名称，例如“VPN Grp”。 然后将“传出接口”设置为“端口”并选择“源”。
27. 在“地址”下，选择“全部”。
28. 在“用户”下，选择第一个 Azure 组。
29. 选择“关闭” > “目标”。  在“地址”下，这通常是内部网络。 选择 **login.microsoft.com** 进行测试。
32. 选择“关闭” > “服务” > “全部”。   然后选择“关闭” > “确定”。 
37. 在左侧菜单中，选择“策略和对象” > “防火墙策略”。 
39. 展开“SSL-VPN 隧道接口(ssl.root)” > “端口” 。
40. 右键单击之前创建的 VPN 策略 (**VPN Grp 1**) 并选择“复制”。
41. 在“VPN 策略”下单击右键并选择“粘贴” > “下方” 。
42. 编辑新策略，为其提供一个不同的名称（例如 **VPN Grp2**）。 同时，更改它应用到的组（更改为另一个 Azure 组）。
43. 右键单击新策略，将状态设置为“已启用”。


## <a name="test-sign-in-by-using-azure"></a>使用 Azure 测试登录

1. 使用私密浏览器会话，转到 `https://<address>`。  
2. 登录应重定向到 Azure AD 进行。
3. 为已分配到 Azure 租户中的 FortiGate 应用的用户提供凭据后，相应的用户门户应会显示。

    ![FortiGate SSL VPN 的屏幕截图](test-sign-in.png)
