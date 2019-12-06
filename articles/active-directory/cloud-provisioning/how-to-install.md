---
title: 安装 Azure AD Connect 云设置代理
description: 本主题分步介绍如何安装预配代理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846226"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>安装 Azure AD Connect 云设置代理
本文档将指导你完成 Azure AD Connect 预配代理的安装过程，以及如何在 Azure 门户中进行初始配置。

>[!IMPORTANT]
>以下安装说明假定满足所有[先决条件](how-to-prerequisites.md)。

安装和配置 Azure AD Connect 设置是在以下步骤中完成的：
    
- [安装代理](#install-the-agent)
- [验证代理安装](#verify-agent-installation)


## <a name="install-the-agent"></a>安装代理

1. 使用企业管理员权限登录到要使用的服务器。
2. 导航到 Azure 门户，选择左侧 Azure Active Directory。
3. 单击 "**管理预配（预览版）** "，然后选择 "**查看所有代理**"。
3. 从 Azure 门户下载 Azure AD Connect 预配代理。
![欢迎屏幕](media/how-to-install/install9.png)</br>
3. 运行 Azure AD Connect 设置（AADConnectProvisioningAgent）
3. 在初始屏幕上，**接受**许可条款，然后单击 "**安装**"。</br>
![欢迎屏幕](media/how-to-install/install1.png)</br>

4. 此操作完成后，将启动配置向导。  用 Azure AD 全局管理员帐户登录。
5. 在 "**连接 Active Directory** " 屏幕上，单击 "**添加目录**"，然后用 Active Directory 管理员帐户登录。  此操作将添加你的本地目录。  单击“下一步”。</br>
![欢迎屏幕](media/how-to-install/install3.png)</br>

6. 在 "**配置完成**" 屏幕上，单击 "**确认**"。  此操作将注册并重新启动代理。</br>
![欢迎屏幕](media/how-to-install/install4.png)</br>

7. 此操作完成后，应会看到已**成功验证**的通知。  可单击 "**退出**"。</br>
![欢迎屏幕](media/how-to-install/install5.png)</br>
8. 如果仍看到初始初始屏幕，请单击 "**关闭**"。


## <a name="verify-agent-installation"></a>验证代理安装
代理验证在 Azure 门户和运行代理的本地服务器上进行。

### <a name="azure-portal-agent-verification"></a>Azure 门户代理验证
若要验证 Azure 是否正在显示代理，请执行以下步骤：

1. 登录到 Azure 门户。
2. 在左侧选择 " **Azure Active Directory**"，单击 " **Azure AD Connect** "，然后在中心选择 "**管理预配（预览版）** "。</br>
![Azure 门户](media/how-to-install/install6.png)</br>

3.  在 " **Azure AD 预配（预览版）** " 屏幕上，单击 "**查看所有代理**"。
![Azure AD 预配](media/how-to-install/install7.png)</br>
 
4. 在 "**本地预配代理" 屏幕**上，你将看到已安装的代理。  验证相关代理是否存在并标记为 "**活动**"。
![预配代理](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>验证端口
若要验证 Azure 是否正在侦听端口443，以及代理是否可以与其通信，可以使用以下内容：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试将验证代理是否能够通过端口443与 Azure 通信。  打开浏览器，并从安装了代理的服务器导航到上述 URL。
![服务](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>在本地服务器上
若要验证代理是否正在运行，请执行以下步骤：

1.  使用管理员帐户登录到服务器
2.  通过导航到服务或转到 "开始/运行"/"服务" 打开**服务**。
3.  在 "**服务**" 下，确保**Microsoft Azure AD 连接代理更新**程序和**Microsoft Azure AD 连接设置代理**存在并且状态为 "**正在运行**"。
![服务](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>已经安装了代理，但必须先对其进行配置和启用，然后才会开始同步用户。  若要配置新代理，请参阅[Azure AD Connect 预配新的代理配置](how-to-configure.md)。



## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
 
