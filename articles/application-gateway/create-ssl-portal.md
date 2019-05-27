---
title: 教程 - 使用 SSL 终端配置应用程序网关 - Azure 门户
description: 在本教程中，你将了解如何使用 Azure 门户配置应用程序网关并为 SSL 终端添加证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134542"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>教程：通过 Azure 门户使用 SSL 终端配置应用程序网关

可通过 Azure 门户使用 SSL 终端的证书配置使用虚拟机作为后端服务器的[应用程序网关](overview.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建自签名证书
> * 使用证书创建应用程序网关
> * 创建用作后端服务器的虚拟机
> * 测试应用程序网关

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

在本部分中，你将使用 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。 为应用程序网关创建侦听器时，需要将该证书上传到 Azure 门户。

在本地计算机上，以管理员身份打开 Windows PowerShell 窗口。 运行以下命令以创建证书：

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

应看到与此响应类似的内容：

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在创建的资源之间实现通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 选择 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
3. 输入 *myAppGateway* 作为应用程序网关的名称，输入 *myResourceGroupAG* 作为新资源组的名称。
4. 接受其他设置的默认值，然后选择“确定”。
5. 依次选择“选择虚拟网络”、“新建”，然后为虚拟网络输入以下值：

   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。

     ![创建虚拟网络](./media/create-ssl-portal/application-gateway-vnet.png)

6. 选择“确定”以创建虚拟网络和子网。
7. 依次选择“选择公共 IP 地址”、“新建”，然后输入公共 IP 地址的名称。 在本示例中，公共 IP 地址名为 *myAGPublicIPAddress*。 接受其他设置的默认值，然后选择“确定”。
8. 选择 **HTTPS** 作为侦听器的协议，并确保将端口定义为 **443**。
9. 选择文件夹图标，然后浏览到以前创建的 *appgwcert.pfx* 证书，以便将其上传。
10. 输入 *mycert1* 作为证书的名称，输入 *Azure123456!*  作为密码，并选择“确定”。

    ![新建应用程序网关](./media/create-ssl-portal/application-gateway-create.png)

11. 检查摘要页上的设置，然后选择“确定”创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 选择左侧菜单中的“所有资源”，然后从资源列表中选择“myVNet”。
2. 依次选择“子网”、“子网”。

    ![创建子网](./media/create-ssl-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后选择“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，你将创建用作应用程序网关的后端服务器的两个虚拟机。 还可以在虚拟机上安装 IIS，以验证是否已成功创建应用程序网关。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 选择“新建”。
2. 选择“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 输入虚拟机的以下值：

    - *myVM* - 作为虚拟机的名称。
    - *azureuser* - 作为管理员用户名。
    - *Azure123456!* - 密码。
    - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。

4. 选择“确定”。
5. 选择“DS1_V2”作为虚拟机的大小，然后选择“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。 
7. 选择“禁用”以禁用启动诊断。
8. 选择“确定”，检查摘要页上的设置，然后选择“创建”。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它设置为 **PowerShell**。

    ![安装自定义扩展](./media/create-ssl-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 输入 *myVM2* 作为其名称，并将其用于 Set-AzVMExtension 中的 VMName。

### <a name="add-backend-servers"></a>添加后端服务器

1. 选择“所有资源”，然后选择“myAppGateway”。
1. 选择“后端池”。 默认池已随应用程序网关自动创建。 选择“appGatewayBackendPool”。
1. 选择“添加目标”将所创建的每个虚拟机添加到后端池。

    ![添加后端服务器](./media/create-ssl-portal/application-gateway-backend.png)

1. 选择“保存”。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 依次选择“所有资源”、“myAGPublicIPAddress”。

    ![记下应用程序网关的公共 IP 地址](./media/create-ssl-portal/application-gateway-ag-address.png)

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 若要接受有关使用自签名证书的安全警告，请依次选择“详细信息”和“继续转到网页”：

    ![安全警告](./media/create-ssl-portal/application-gateway-secure.png)

    随即显示受保护的 IIS 网站，如下例所示：

    ![在应用程序网关中测试基 URL](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关如何使用 Azure 应用程序网关的详细信息](application-gateway-introduction.md)
