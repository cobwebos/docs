---
title: "在 Azure Stack 上的应用服务中启用 FTP | Microsoft 文档"
description: "在 Azure Stack 上的应用服务中启用 FTP 所需完成的步骤"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>在 Azure Stack 上的应用服务中启用 FTP

成功将应用服务部署到 Azure Stack 上后，如果想要启用 FTP 发布，以便租户可以上传其应用程序文件和内容，需要完成一些附加步骤。  在未来版本中，会自动执行这些步骤。

> [!NOTE]
> 这些步骤适用于在 Azure Stack 资源提供程序上配置应用服务的服务或企业管理员。

## <a name="enable-ftp"></a>启用 FTP

1.  以服务管理员身份登录到 Azure Stack 门户。
2.  浏览到“网络接口”，在“资源组” - **AppService-LOCAL** 下选择 **FTP-NIC**。 ![Azure Stack 网络接口][1]
3.  记下 **FTP-NIC** 的**公共 IP 地址**。 
![Azure Stack 网络接口详细信息][2]
4.  接下来，浏览到“虚拟机”，选择 **FTP0-VM**。 ![Azure Stack 虚拟机][3]
5.  使用“连接”按钮打开与 VM 的远程桌面会话，并使用在应用服务部署期间设置的管理员凭据登录到该会话。  
![Azure Stack 虚拟机详细信息][4]
6.  在 FTP VM (FTP0-VM) 上打开 **Internet Information Service (IIS) Manager**。
7.  在“站点”下选择“托管 FTP 站点”。
8.  打开“FTP 防火墙支持”。 ![应用服务 FTP0-VM 上的 IIS Manager][5]
9.  输入 FTP-NIC 的公共 IP 地址，单击“应用”![IIS Manager FTP 防火墙支持][6]

## <a name="validate-the-enabling-of-ftp"></a>验证是否启用了 FTP

1.  以服务管理员或租户身份登录到 Azure Stack 门户。
2.  浏览到“应用服务”，选择已创建的 Web 应用、移动应用或 API 应用。 ![应用服务][7]
3.  在应用程序详细信息中，记下 **FTP 主机名**和 **FTP/部署用户名**。 ![应用服务应用详细信息][8]
> [!NOTE]
> 如果在“FTP/部署用户名”下看不到条目，则先需要使用“部署凭据”边栏选项卡设置部署凭据。

4.  打开 Windows 资源管理器，在文件地址栏中输入 FTP 主机名，例如 ftp://ftp.appservice.azurestack.local
5.  出现提示时输入步骤 3 中记下的**部署凭据**，如果该功能已启用，会看到应用服务应用程序的内容目录列表。 ![FTP 文件列表][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
