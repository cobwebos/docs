---
title: 教程：使用门户实现基于 URL 路径的路由规则 - Azure 应用程序网关
description: 在本教程中，你将了解如何使用 Azure 门户为应用程序网关和虚拟机规模集创建基于 URL 路径的路由规则。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: bc810ac7901d83f03d3f3ac2199561225326d261
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048136"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>教程：通过 Azure 门户使用基于路径的路由规则创建应用程序网关

创建[应用程序网关](application-gateway-introduction.md)时，可以使用 Azure 门户配置[基于 URL 路径的路由规则](application-gateway-url-route-overview.md)。 本教程中使用虚拟机创建后端池。 然后创建路由规则，以确保 Web 流量到达池中的相应服务器。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建应用程序网关
> * 为后端服务器创建虚拟机
> * 使用后端服务器创建后端池
> * 创建后端侦听器
> * 创建基于路径的路由规则

![URL 路由示例](./media/application-gateway-create-url-route-portal/scenario.png)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户

## <a name="create-virtual-machines"></a>创建虚拟机

本示例将创建三个虚拟机，用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证应用程序网关是否按预期运行。

1. 在 Azure 门户中，选择“创建资源”。 
2. 在“常用”列表中选择“Windows Server 2016 Datacenter”  。
3. 输入虚拟机的以下值：

    - **资源组**，选择“新建”  ，然后键入“myResourceGroupAG”。 
    - **虚拟机名称**：*myVM1*
    - **区域**： *（美国）美国东部*
    - **用户名**：*azureuser*
    - **密码**：*Azure123456!* -


4. 选择“下一步:磁盘”  。
5. 选择“下一步:网络” 
6. 对于“虚拟网络”，请选择“新建”，然后键入虚拟网络的以下值：  

   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myBackendSubnet*，第一个子网名称
   - *10.0.1.0/24* - 子网地址空间。
   - *myAGSubnet* - 第二个子网名称。
   - *10.0.0.0/24* - 子网地址空间。
7. 选择“确定”  。

8. 确保在“网络接口”  下，为子网选择了“myBackendSubnet”，  然后选择“下一步:  管理”。
9. 选择“关闭”  以禁用启动诊断。
10. 单击“查看 + 创建”，检查摘要页上的设置，然后选择“创建”。  
11. 再创建两个虚拟机 *myVM2* 和 *myVM3*，然后将其置于 *MyVNet* 虚拟网络和 *myBackendSubnet* 子网。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它设置为 **PowerShell**。

    ![安装自定义扩展](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. 使用刚刚完成的步骤创建另外两个虚拟机并安装 IIS。 在 Set-AzVMExtension 中输入 *myVM2* 和 *myVM3* 作为名称，并输入 VMName 值。

## <a name="create-an-application-gateway"></a>创建应用程序网关

1. 选择 Azure 门户左侧菜单上的“创建资源”  。 此时会显示“新建”窗口。 

2. 选择“网络”  ，然后在“特色”列表中选择“应用程序网关”   。

### <a name="basics-tab"></a>“基本信息”选项卡

1. 在“基本信息”选项卡上，输入这些值作为以下应用程序网关设置  ：

   - **资源组**：选择 **myResourceGroupAG** 作为资源组。
   - **应用程序网关名称**：输入 *myAppGateway* 作为应用程序网关的名称。
   - **区域** - 选择“(US) 美国东部”  。

        ![新建应用程序网关：基础](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  在“配置虚拟网络”下，选择“myVNet”作为虚拟网络的名称。  
3. 选择“myAGSubnet”  作为子网。
3. 接受其他设置的默认值，然后选择“下一步:  前端”。

### <a name="frontends-tab"></a>“前端”选项卡

1. 在“前端”选项卡上，验证“IP 地址类型”是否设置为“公共”    。

   > [!NOTE]
   > 对于应用程序网关 v2 SKU，只能选择**公共**前端 IP 配置。 目前尚未为此 v2 SKU 启用专用前端 IP 配置。

2. 为“公共 IP 地址”选择“新建”，输入“myAGPublicIPAddress”作为公共 IP 地址名称，然后选择“确定”     。 
3. 在完成时选择“下一步:**后端**。

### <a name="backends-tab"></a>“后端”选项卡

后端池用于将请求路由到为请求提供服务的后端服务器。 后端池可以包含 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。

1. 在“后端”选项卡上，选择“+添加后端池”   。

2. 在打开的“添加后端池”窗口中，输入以下值以创建空的后端池  ：

    - **名称**：输入“myBackendPool”作为后端池的名称  。
3. 在“后端目标”  、“目标类型”  下，从下拉列表中选择“虚拟机”  。

5. 在  “目标”下， 为 myVM1 选择网络接口  。
6. 选择 **添加** 。
7. 重复此步骤，添加“图片”后端池，   myVM2 作为目标；添加“视频”后端池，   myVM3 作为目标。
8. 选择“添加”以保存后端池配置并返回到“后端”选项卡  。 

4. 在“后端”  选项卡上，选择“下一步:  配置”。

### <a name="configuration-tab"></a>配置选项卡

在“配置”选项卡上，将连接使用传递规则创建的前端和后端池  。

1. 选择“传递规则”列中的“添加规则”   。

2. 在打开的“添加传递规则”窗口中，输入“myRoutingRule”作为规则名称    。

3. 传递规则需要侦听器。 在“添加传递规则”窗口中的“侦听器”选项卡上，输入侦听器的以下值   ：

    - **侦听器名称**：输入“myListener”作为侦听器名称  。
    - **前端 IP**：选择“公共”，以选择为前端创建的公共 IP  。
    - **端口**：类型 *8080*
  
        接受“侦听器”选项卡上其他设置的默认值，然后选择“后端目标”选项卡以配置剩余的传递规则   。

4. 在“后端目标”选项卡上，为“后端目标”选择“myBackendPool”    。

5. 对于“HTTP 设置”，选择“新建”以创建新的 HTTP 设置   。 HTTP 设置将决定传递规则的行为。 

6. 在打开的“添加 HTTP 设置”窗口中，为“HTTP 设置名称”输入“myHTTPSetting”    。 接受“添加 HTTP 设置”窗口中其他设置的默认值，然后选择“添加”以返回到“添加传递规则”窗口    。
7. 在“基于路径的路由”下  ，选择“添加多个目标以创建基于路径的规则”  。
8. 对于  “路径”，请键入 */images/* \*。
9. 对于“路径规则名称”  ，请键入“图片”  。
10. 对于“HTTP 设置”  ，选择“myHTTPSetting” 
11. 对于“后端目标”  ，选择“图片”  。
12. 选择  “添加”以保存路径规则，并返回“添加路由规则”  选项卡。
13. 重复此步骤为视频添加其他规则。
14. 选择“添加”  添加路由规则并返回“配置”  选项卡。
15. 在完成时选择“下一步:  标记”，然后选择“下一步:  查看 + 创建”。

> [!NOTE]
> 不需要添加自定义 */* * 路径规则来处理默认情况。 这会由默认后端池自动处理。

### <a name="review--create-tab"></a>“查看 + 创建”选项卡

复查“查看 + 创建”选项卡上的设置，然后选择“创建”以创建虚拟网络、公共 IP 地址和应用程序网关   。 Azure 可能需要数分钟时间来创建应用程序网关。 请等待部署成功完成，然后再前进到下一部分。


## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 选择“所有资源”，然后选择“myAppGateway”。  

    ![记下应用程序网关的公共 IP 地址](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如，http:\//52.188.72.175:8080。

    ![在应用程序网关中测试基 URL](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   端口 8080 上的侦听器将此请求路由到默认的后端池。

3. 将 URL 更改为 *http://&lt;ip-address&gt;:8080/images/test.htm*（请将 &lt;ip-address&gt; 替换为你的 IP 地址），然后，应会看到如以下示例所示的内容：

    ![在应用程序网关中测试映像 URL](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   端口 8080 上的侦听器将此请求路由到“图片”  后端池。

4. 将 URL 更改为 *http://&lt;ip-address&gt;:8080/video/test.htm*（请将 &lt;ip-address&gt; 替换为你的 IP 地址），然后，应会看到如以下示例所示的内容：

    ![在应用程序网关中测试视频 URL](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   端口 8080 上的侦听器将此请求路由到“视频”  后端池。


## <a name="next-steps"></a>后续步骤

- [在 Azure 应用程序网关上启用端到端 SSL](application-gateway-backend-ssl.md)
