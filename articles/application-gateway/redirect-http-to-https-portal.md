---
title: 使用 Azure 门户创建支持 HTTP 到 HTTPS 重定向的应用程序网关
description: 了解如何使用 Azure 门户创建支持从 HTTP 到 HTTPS 重定向流量的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 1d30ddfb97b065d0d2fdf3bf91a73d3f7eb1b70f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110725"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>使用 Azure 门户创建支持 HTTP 到 HTTPS 重定向的应用程序网关

可以通过 Azure 门户使用 SSL 终端的证书创建[应用程序网关](overview.md)。 路由规则用于将 HTTP 流量重定向到应用程序网关中的 HTTPS 端口。 在此示例中，还会为包含两个虚拟机实例的应用程序网关的后端池创建一个[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建自签名证书
> * 设置网络
> * 使用证书创建应用程序网关
> * 添加侦听器和重定向规则
> * 使用默认后端池创建虚拟机规模集

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本教程需要 Azure PowerShell 模块 3.6 或更高版本以创建证书并安装 IIS。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 若要运行本教程中的命令，还需要运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-self-signed-certificate"></a>创建自签名证书

为供生产使用，应导入由受信任的提供程序签名的有效证书。 对于本教程，请使用 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。 可以结合返回的指纹使用 [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate)，从证书导出 pfx 文件。

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

应会显示如下结果所示的内容：

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

使用指纹创建 pfx 文件：

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在创建的资源之间实现通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 在 [http://portal.azure.com](http://portal.azure.com) 中登录 Azure 门户。
2. 单击 Azure 门户左上角的“创建资源”。
3. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
4. 输入应用程序网关的以下值：

    - *myAppGateway* - 应用程序网关的名称。
    - *myResourceGroupAG* - 新资源组。

    ![新建应用程序网关](./media/create-url-route-portal/application-gateway-create.png)

5. 接受其他设置的默认值，然后单击“确定”。
6. 依次单击“选择虚拟网络”、“新建”，然后输入虚拟网络的以下值：

    - *myVNet* - 虚拟网络的名称。
    - *10.0.0.0/16* - 虚拟网络地址空间。
    - *myAGSubnet* - 子网名称。
    - *10.0.1.0/24* - 子网地址空间。

    ![创建虚拟网络](./media/create-url-route-portal/application-gateway-vnet.png)

7. 单击“确定”创建虚拟网络和子网。
8. 在“前端 IP 配置”下，确保“IP 地址类型”设置为“公用”，并且选择了“新建”。 输入 myAGPublicIPAddress 作为名称。 接受其他设置的默认值，然后单击“确定”。
9. 在“侦听器配置”下，选择“HTTPS”，然后选择“选择文件”导航到 c:\appgwcert.pfx 文件并选择“打开”。
10. 输入 appgwcert 作为证书名称并输入 Azure123456! 密码。
11. 将 Web 应用程序防火墙保持为已禁用状态，然后选择“确定”。
12. 检查摘要页上的设置，然后选择“确定”创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 选择左侧菜单中的“所有资源”，然后从资源列表中选择“myVNet”。
2. 选择“子网”，然后单击“子网”。

    ![创建子网](./media/create-url-route-portal/application-gateway-subnet.png)

3. 输入 myBackendSubnet 作为子网名称。
4. 输入 10.0.2.0/24 作为地址范围，然后选择“确定”。

## <a name="add-a-listener-and-redirection-rule"></a>添加侦听器和重定向规则

### <a name="add-the-listener"></a>添加侦听器

首先，为端口 80 添加名为 myListener 的侦听器。

1. 打开“myResourceGroupAG”资源组，然后选择“myAppGateway”。
2. 选择“侦听器”，然后选择“+ 基本”。
3. 输入 MyListener 作为名称。
4. 输入 httpPort 作为新的前端端口名称，并输入 80 作为端口。
5. 确保将协议设置为“HTTP”，然后选择“确定”。

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>添加具有重定向配置的路由规则

1. 在“myAppGateway”上，选择“规则”，然后选择“+基本”。
2. 对于“名称”，输入 Rule2。
3. 确保对于侦听器选择 MyListener。
4. 选中“配置重定向”复选框。
5. 对于“重定向类型”，选择“永久”。
6. 对于“重定向目标”，选择“侦听器”。
7. 确保“目标侦听器”设置为“appGatewayHttpListener”。
8. 选中“包含查询字符串”和“包含路径”复选框。
9. 选择“确定”。

## <a name="create-a-virtual-machine-scale-set"></a>创建虚拟机规模集

在此示例中，将创建虚拟机规模集，以便为应用程序网关的后端池提供服务器。

1. 在门户左上角，选择“+创建资源”。
2. 选择“计算”。
3. 在搜索框中，输入“规模集”，并按 Enter。
4. 选择“虚拟机规模集”，然后选择“创建”。
5. 对于“虚拟机规模集名称”，输入 myvmss。
6. 对于操作系统磁盘映像，**确保选中“Windows Server 2016 Datacenter”。
7. 对于“资源组”，选择 myResourceGroupAG。
8. 对于“用户名”，输入 azureuser。
9. 对于“密码”，输入 Azure123456! 并确认该密码。
10. 对于“实例计数”，确保值为“2”。
11. 对于“实例大小”，选择“D2s_v3”。
12. 在“联网”下，确保“选择负载均衡选项”设置为“应用程序网关”。
13. 确保“应用程序网关”设置为 myAppGateway。
14. 确保“子网”设置为 myBackendSubnet。
15. 选择“创建”。

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>将规模集与正确的后端池关联

虚拟机规模集门户 UI 可为规模集创建新后端池，但是要将它与现有 appGatewayBackendPool 关联。

1. 打开“myResourceGroupAg”资源组。
2. 选择“myAppGateway”。
3. 选择“后端池”。
4. 选择“myAppGatewaymyvmss”。
5. 选择“从后端池中删除所有目标”。
6. 选择“保存”。
7. 此过程完成之后，选择“myAppGatewaymyvmss”后端池，选择“删除”，然后选择“确定”以确认。
8. 选择“appGatewayBackendPool”。
9. 在“目标”下，选择“VMSS”。
10. 在“VMSS”下，选择“myvmss”。
11. 在“网络接口配置”下，选择“myvmssNic”。
12. 选择“保存”。

### <a name="upgrade-the-scale-set"></a>升级规模集

最后，必须使用这些更改升级规模集。

1. 选择“myvmss”规模集。
2. 在“设置”下，选择“实例”。
3. 选择两个实例，然后选择“升级”。
4. 请选择“是”以确认。
5. 此操作完成之后，返回到“myAppGateway”，然后选择“后端池”。 现在应看到“appGatewayBackendPool”具有两个目标，而“myAppGatewaymyvmss”没有目标。
6. 选择“myAppGatewaymyvmss”，然后选择“删除”。
7. 选择“确定”以确认。

### <a name="install-iis"></a>安装 IIS

在规模集上安装 IIS 的一种简单方法是使用 PowerShell。 在门户中，单击 Cloud Shell 图标，并确保选择了“PowerShell”。

将以下代码粘贴到 PowerShell 窗口中，然后按 Enter。

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>升级规模集

使用 IIS 更改实例之后，必须再次使用此更改升级规模集。

1. 选择“myvmss”规模集。
2. 在“设置”下，选择“实例”。
3. 选择两个实例，然后选择“升级”。
4. 请选择“是”以确认。

## <a name="test-the-application-gateway"></a>测试应用程序网关

可以从应用程序网关“概述”页面获取应用程序公共 IP 地址。

1. 选择“myAppGateway”。
2. 在“概述”页面上，记下“前端公共 IP 地址”下的 IP 地址。

3. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如： http://52.170.203.149

   ![安全警告](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. 若要接受有关使用自签名证书的安全警告，请依次选择“详细信息”和“继续转到网页”。 随即显示受保护的 IIS 网站，如下例所示：

   ![在应用程序网关中测试基 URL](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>后续步骤

了解如何[创建支持内部重定向的应用程序网关](redirect-internal-site-powershell.md)。