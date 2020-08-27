---
title: FortiGate 部署指南 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 FortiGate SSL VPN 之间配置单一登录。
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
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657027"
---
# <a name="fortigate-deployment-guide"></a>FortiGate 部署指南

## <a name="contents"></a>目录

- 兑换 FortiGate 许可证
- 下载固件
- 部署 FortiGate VM
   - 设置静态公共 IP 地址并分配完全限定的域名
   - 为 TCP 端口创建新的入站网络安全组规则
- 为 FortiGate 创建自定义 Azure 应用
- 为组匹配做准备
   - 为用户创建组
- 配置 FortiGate VM
   - 安装许可证
   - 更新固件
   - 将管理端口更改为 TCP
   - 上传 Azure Active Directory SAML 签名证书
   - 上传和配置自定义 SSL 证书
   - 执行命令行配置
   - 创建 VPN 门户和防火墙策略
- 使用 Azure 测试登录

## <a name="redeeming-the-fortigate-license"></a>兑换 FortiGate 许可证

Fortinet FortiGate 下一代防火墙产品在 Azure IaaS 中作为虚拟机提供。 此虚拟机有两种授权模式 -

- 即用即付 (PAYG)
- 自带许可证 (BYOL)

与 Fortinet 合作提供安全混合访问 (SHA) 指导时，Azure AD Get to Production SHA 团队的成员可得到 Fortinet 提供的许可证。 如果未提供任何许可证，PAYG 部署也将正常运行。

如果已颁发许可证，Fortinet 提供了一个必须联机兑换的注册代码

![Fortigate SSL VPN](registration-code.png)

1. 在 https://support.fortinet.com/ 注册
2. 注册后，在 https://support.fortinet.com/ 登录
3. 导航到“资产”- >“注册/激活” 
4. 输入 Fortinet 提供的注册代码
5. 指定该注册代码，选择“该产品将由非政府用户使用”，然后单击“下一步” 
6. 输入产品说明（例如 FortiGate），将 Fortinet 合作伙伴设置为“其他”- >“Microsoft”，然后单击“下一步”  
7. 接受“Fortinet 产品注册协议”并单击“下一步” 
8. 接受“条款”，然后单击“确认” 
9. 单击“许可证文件下载”并保存以供之后使用


## <a name="download-firmware"></a>下载固件

编写时，Fortinet FortiGate Azure VM 不附带进行 SAML 身份验证所需的固件版本。 必须从 Fortinet 获取最新版本。

1. 在 https://support.fortinet.com/ 登录
2. 导航到“下载”- >“固件映像” 
3. 单击“发行说明”右侧的“下载” 
4. 单击“v6”。
5. 单击“6”。
6. 单击“6.4”。
7. 单击同一行上的 HTTPS 链接，下载“FGT_VM64_AZURE-v6-build1637-FORTINET.out” 
8. 保存该文件以供之后使用


## <a name="deploy-the-fortigate-vm"></a>部署 FortiGate VM

1. 导航到 https://portal.azure.com 并登录到要在其中部署 FortiGate 虚拟机的订阅
2. 创建新的资源组，或打开要在其中部署 FortiGate 虚拟机的资源组
3. 单击“添加”
4. 在“搜索市场”对话框中输入“Forti”，然后选择“Fortinet FortiGate 下一代防火墙”  
5. 选择软件计划（如果你有许可证，则选择 BYOL，否则选择 PAYG），然后单击“创建”
6. 填充 VM 配置

    ![Fortigate SSL VPN](virtual-machine.png)

7. 将身份验证类型设置为“密码”并提供 VM 的管理凭据
8. 单击“查看 + 创建”
9. 单击“创建” 
10. 等待 VM 部署完成


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>设置静态公共 IP 地址并分配完全限定的域名

为获得一致的用户体验，最好将分配给 FortiGate VM 的公共 IP 地址设置为静态分配。 此外，将其映射到完全限定的域名也有助于获得一致的用户体验。

设置静态公共 IP 地址

1. 导航到 https://portal.azure.com 并打开 FortiGate VM 的设置
2. 在“概述”屏幕上，单击该公共 IP 地址

    ![Fortigate SSL VPN](public-ip-address.png)

3. 单击“静态”，然后单击“保存” 

分配完全限定的域名

如果你拥有要在其中部署 FortiGate VM 的环境的公共可路由域名，请为 VM 创建一个映射到上面静态分配的公共 IP 地址的主机 (A) 记录。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>为 TCP 端口创建新的入站网络安全组规则

1. 导航到 https://portal.azure.com 并打开 FortiGate VM 的设置
2. 在左侧菜单中，单击“网络”。 将列出网络接口并显示入站端口规则
3. 单击“添加入站端口规则”
4. 为 TCP 8443 创建新的入站端口规则

    ![Fortigate SSL VPN](port-rule.png)

5. 单击“添加”


## <a name="create-a-custom-azure-app-for-fortigate"></a>为 FortiGate 创建自定义 Azure 应用

1. 导航到 https://portal.azure.com 并为租户打开将提供 FortiGate 登录标识的 Azure Active Directory 边栏选项卡
2. 在左侧菜单中，单击“企业应用程序”
3. 单击“新建应用程序”
4. 单击“非库应用程序”
5. 提供一个名称（如 FortiGate），然后单击“添加”
6. 在左侧菜单中，单击“用户和组”
7. 添加能够登录的用户，然后单击“分配”
8. 在左侧菜单中，单击“单一登录”
9. 单击“SAML”
10. 在“基本 SAML 配置”下，单击铅笔以编辑配置
11. 配置
    - 标识符（实体 ID）为 `https://<address>/remote/saml/metadata`
    - 回复 URL（断言使用者服务 URL）为 `https://<address>/remote/saml/login`
    - 注销 URL 为 `https://<address>/remote/saml/logout`

    其中 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

    记录每个 URL 以供之后使用 -

    - 实体 ID
    - 回复 URL
    - 注销 URL
12. 单击“保存” 
13. 关闭“基本 SAML 配置”
14. 在“3 – SAML 签名证书”下，下载“证书(Base64)”并将其保存以供之后使用 
15. 在“4 – 设置(应用名称)”下，复制 Azure 登录 URL、Azure AD 标识符以及 Azure 注销 URL，并将它们保存以供之后使用
    - Azure 登录 URL
    - Azure AD 标识符
    - Azure 注销 URL
16. 在“2 – 用户特性和声明”下，单击铅笔以编辑配置
17. 单击“添加新声明”
18. 将“名称”设置为“username”
19. 将“源属性”设置为“user.userprincipalname”
20. 单击“保存” 
21. 单击“添加组声明”
22. 选择“所有组”
23. 勾选“自定义组声明的名称”
24. 将“名称”设置为“group”
25. 单击“保存” 


## <a name="prepare-for-group-matching"></a>为组匹配做准备

登录后，FortiGate 允许基于组成员身份提供不同的用户门户体验。 例如，可能会针对营销组提供一种体验，而针对财务组提供另一种体验。

按如下所述进行配置 -

### <a name="create-groups-for-users"></a>为用户创建组

1. 导航到 https://portal.azure.com 并为租户打开将提供 FortiGate 登录标识的 Azure Active Directory 边栏选项卡
2. 单击“组”
3. 单击“新建组”
4. 创建一个组，设置如下：
    - 组类型 = 安全
    - 组名 = `a meaningful name`
    - 组说明 = `a meaningful description for the group`
    - 成员身份类型 = 已分配
    - 成员 = `users for the user experience that will map to this group`
5. 对任何其他用户体验重复步骤 3 和 4
6. 创建组后，选择每个组并记录其对象 ID
7. 保存这些对象 ID 和组名以供之后使用


## <a name="configure-the-fortigate-vm"></a>配置 FortiGate VM

### <a name="install-the-license"></a>安装许可证

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 忽略任何证书错误后继续
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
4. 如果部署使用 BYOL 模型，一个上传许可证的提示随即显示。 选择之前创建的许可证文件并将其上传，单击“确定”并重启 FortiGate VM -

    ![Fortigate SSL VPN](license.png)

5. 重启后，请通过管理员凭据再次登录，以验证许可证

### <a name="update-firmware"></a>更新固件

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 忽略任何证书错误后继续
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
4. 在左侧菜单中，单击“系统”
5. 在左侧菜单的“系统”下，选择“固件”
6. 在“固件管理”页中，单击“浏览”并选择之前下载的固件文件
7. 忽略警告，并单击“备份配置并升级”

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. 单击“继续”
9. 当系统提示保存 FortiGate 配置（作为 .conf 文件）时，请单击“保存”
10. 等待固件上传和应用，并等待 FortiGate VM 重启
11. FortiGate VM 重启后，请使用管理员凭据再次登录
12. 当系统提示执行仪表板设置时，单击“稍后”
13. 教程视频开始播放时，单击“确定”

### <a name="change-the-management-port-to-tcp"></a>将管理端口更改为 TCP

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 忽略任何证书错误后继续
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
4. 在左侧菜单中，单击“系统”
5. 在“管理设置”下，将 HTTPS 端口更改为“8443”
6. 单击“应用”
7. 应用更改后，浏览器将尝试重载管理页面，但它将失败。 从现在开始，管理页面地址将是 `https://<address>`

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>上传 Azure Active Directory SAML 签名证书

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 忽略任何证书错误后继续
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
4. 在左侧菜单中，单击“系统”
5. 在“系统”下，单击“证书”
6. 单击“导入”- >“远程证书” 
7. 浏览到从 Azure 租户中 FortiGate 自定义应用部署下载的证书，将其选中，然后单击“确定”

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>上传和配置自定义 SSL 证书

你可能希望使用自己的 SSL 证书（该证书支持你使用的 FQDN）配置 FortiGate VM。 如果你有权访问以 .PFX 格式与私钥打包在一起的 SSL 证书，则该证书可用于此目的

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 忽略任何证书错误后继续
3. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
4. 在左侧菜单中，单击“系统”
5. 在“系统”下，单击“证书”
6. 单击“导入”- >“本地证书” 
7. 单击“PKCS #12 证书”
8. 浏览到包含 SSL 证书和私钥的 .PFX 文件
9. 提供 .PFX 密码
10. 为证书提供一个有意义的名称
11. 单击 **“确定”**
12. 在左侧菜单中，单击“系统”
13. 在“系统”下，单击“设置”
14. 在“管理设置”下，展开 HTTPS 服务器证书旁的下拉列表并选择前面导入的 SSL 证书
15. 单击“应用”
16. 关闭浏览器窗口，然后再次导航到 `https://<address>`
17. 使用 FortiGate 管理员凭据登录，并观察正在使用的正确 SSL 证书


### <a name="perform-command-line-configuration"></a>执行命令行配置

执行用于 SAML 身份验证的命令行配置

1. 导航到 https://portal.azure.com 并打开 FortiGate VM 的设置
2. 在左侧菜单中，单击“串行控制台”
3. 在串行控制台使用 FortiGate VM 管理员凭据登录

    在下一步中，将需要前面记录的 URL。 即 -

    - 实体 ID
    - 回复 URL
    - 注销 URL
    - Azure 登录 URL
    - Azure AD 标识符
    - Azure 注销 URL
4. 在串行控制台执行以下命令 -

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
    > Azure 注销 URL 包含一个 ? 符号。 这需要一个特殊的键序列才能将其正确提供给 FortiGate 串行控制台。 该 URL 通常为 -

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    若要在串行控制台中提供此项，请键入

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    然后按 CTRL+V，

    再粘贴 URL 的剩余部分以完成该行

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. 若要确认配置，请执行 -

    ```
    show user saml
    ```

执行用于组匹配的命令行配置

1. 导航到 https://portal.azure.com 并打开 FortiGate VM 的设置
2. 在左侧菜单中，单击“串行控制台”
3. 在串行控制台使用 FortiGate VM 管理员凭据登录
4. 在串行控制台执行以下命令 -

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

    对于每个在 FortiGate 中具有不同门户体验的附加组，从 edit `group 1 name` 开始重复这些命令

执行用于身份验证超时的命令行配置

1. 导航到 https://portal.azure.com 并打开 FortiGate VM 的设置
2. 在左侧菜单中，单击“串行控制台”
3. 在串行控制台使用 FortiGate VM 管理员凭据登录
4. 在串行控制台执行以下命令 -

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>创建 VPN 门户和防火墙策略

1. 导航到 `https://<address>`

    这里的 `address` 是分配给 FortiGate VM 的 FQDN 或公共 IP 地址

2. 使用在 FortiGate VM 部署期间提供的管理员凭据登录
3. 在左侧菜单中，单击“VPN”
4. 在“VPN”下，单击“SSL-VPN 门户”
5. 单击“新建”
6. 提供一个名称（通常与用于提供自定义门户体验的 Azure 组相匹配）
7. 单击“源 IP 池”旁边的加号 (+)，选择默认池，然后单击“关闭” 
8. 自定义此组的体验。 对于测试，这可以是对门户消息和主题的自定义。 你也可在此处创建将用户定向到内部资源的自定义书签
9. 单击 **“确定”**
10. 对于将具有自定义门户体验的每个 Azure 组，重复步骤 5 到 9
11. 在“VPN”下，单击“SSL-VPN 设置”
12. 单击“侦听接口”旁边的加号 (+)
13. 选择“Port1”，然后单击“关闭” 


14. 如果之前安装了自定义 SSL 证书，请在下拉菜单中更改服务器证书以使用自定义 SSL 证书
15. 在“身份验证/门户映射”下，单击“新建”
16. 选择第一个 Azure 组，并将其与相同名称的门户匹配
17. 单击 **“确定”**
18. 对于每个 Azure 组/门户对，重复步骤 15 到 17
19. 在“身份验证/门户映射”下，编辑“所有其他用户/组”
20. 将门户设置为“完全访问”
21. 单击 **“确定”**
22. 单击“应用”
23. 滚动到“SSL-VPN 设置”页的顶部，单击警告“不存在 SSL-VPN 策略
     **。** 单击此处以使用这些设置创建新的 SSL-VPN 策略”
24. 提供一个名称，例如“VPN Grp”
25. 将传出接口设置为“端口”
26. 单击“源”
27. 在“地址”下，选择“全部”
28. 在“用户”下，选择第一个 Azure 组
29. 单击 **“关闭”**。
30. 单击“目标”
31. 在“地址”下，这通常是内部网络。 选择 login.microsoft.com 进行测试
32. 单击 **“关闭”**。
33. 单击“服务”
34. 单击“全部”
35. 单击 **“关闭”**。
36. 单击 **“确定”**
37. 在左侧菜单中，单击“策略和对象”
38. 在“策略和对象”下，单击“防火墙策略”
39. 展开“SSL-VPN 隧道接口(ssl.root)”->“端口”
40. 右键单击之前创建的 VPN 策略 (VPN Grp 1) 并选择“复制” 
41. 在“VPN 策略”下单击右键并选择“粘贴”->“下方” 
42. 编辑新策略，为其提供不同的名称（例如 VPN Grp2），并更改适用的组（另一个 Azure 组）
43. 右键单击新策略，将状态设置为“已启用”


## <a name="test-sign-in-using-azure"></a>使用 Azure 测试登录

1. 使用私密浏览器会话，导航到 `https://<address>` 
2. “登录”应重定向到 Azure Active Directory 以进行登录
3. 为已分配到 Azure 租户中 FortiGate 应用的用户提供凭据后，相应的用户门户应会显示

    ![Fortigate SSL VPN](test-sign-in.png)
