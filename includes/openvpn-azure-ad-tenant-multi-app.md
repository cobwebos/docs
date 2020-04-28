---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77485568"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.创建 Azure AD 租户

使用[创建新租户](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)一文中的步骤创建 Azure AD 租户：

* 组织名称
* 初始域名

  示例：

   ![新 Azure AD 租户](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2.创建租户用户

在此步骤中，你将创建两个 Azure AD 租户用户：一个全局管理员帐户和一个主要用户帐户。 主要用户帐户用作主要嵌入帐户（服务帐户）。 创建 Azure AD 租户用户帐户时，可以根据要创建的用户类型调整目录角色。 使用[此文](../articles/active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤为 Azure AD 租户创建至少两个用户。 若要创建帐户类型，请务必更改“目录角色”： 

* 全局管理员
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3.注册 VPN 客户端

在 Azure AD 租户中注册 VPN 客户端。

1. 找到要用于身份验证的目录的目录 ID。 此 ID 在“Active Directory”页的“属性”部分中列出。

    ![Directory ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. 复制“目录 ID”。

3. 以拥有“全局管理员”角色的用户身份登录到 Azure 门户。 

4. 接下来，做出管理员许可。 在浏览器的地址栏中复制并粘贴与部署位置相关的 URL：

    公共

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    德国 Microsoft 云

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure 中国世纪互联

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. 出现提示时，请选择“全局管理员”帐户。 

    ![Directory ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. 出现提示时，请选择“接受”。 

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. 在 Azure AD 下的“企业应用程序”中，将会发现已列出“Azure VPN”。  

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4.注册其他应用程序

在此步骤中，你将为各个用户和组注册其他应用程序。

1. 在 Azure Active Directory 下，依次单击“应用注册”、“+ 新建注册”。  

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. 在“注册应用程序”页上，输入名称。   选择所需“支持的帐户类型”  ，然后单击“注册”  。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. 注册新应用后，单击应用边栏选项卡下的“公开 API”。 

4. 单击“+ 添加作用域”。 

5. 保留默认的“应用程序 ID URI”  。 单击“保存并继续”  。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. 填写必填字段，确保“状态”为“已启用”。   单击“添加作用域”。 

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. 单击“公开 API”  ，然后单击“+ 添加客户端应用程序”。   对于“客户端 ID”，请根据云输入以下值： 

    - 对于“Azure 公共”  ，请输入 **41b23e61-6c1e-4545-b367-cd054e0ed4b4**
    - 对于“Azure 政府”  ，请输入 **51bb15d4-3a4f-4ebf-9dca-40096fe32426**
    - 对于“Azure 德国”  ，请输入 **538ee9e6-310a-468d-afef-ea97365856a9**
    - 对于“Azure 中国世纪互联”，请输入 **49f817b6-84ae-4cc0-928c-73f27289b3aa** 

8. 单击“添加应用程序”  。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. 复制“概述”页中的“应用程序(客户端) ID”。   你需要使用此信息来配置 VPN 网关。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. 重复[注册其他应用程序](#register-apps)中的步骤，根据安全要求创建所需数量的应用程序。 每个应用程序将关联到某个 VPN 网关，并可以包含一组不同的用户。 只能将一个应用程序关联到一个网关。

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5.将用户分配给应用程序

将用户分配到应用程序。

1. 在 Azure AD 下的“企业应用程序”中，选择新注册的应用程序并单击“属性”。   确保“需要用户分配?”设置为“是”。   单击“保存”  。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. 在应用页上，依次单击“用户和组”、“+添加用户”。  

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. 在“添加分配”下，单击“用户和组”。   选择希望其能够访问此 VPN 应用程序的用户。 单击“选择”。 

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)