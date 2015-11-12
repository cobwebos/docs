<properties
   pageTitle="在 Azure 门户中为 VM 创建 FQDN | Microsoft Azure"
   description="了解如何在 Azure 预览门户中基于虚拟机为资源管理器创建完全限定域名或 FQDN。"
   services="virtual-machines"
   documentationCenter=""
   authors="dsk-2015"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.date="08/21/2015"
   wacn.date=""/>

# 在 Azure 预览门户中创建完全限定域名

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-include.md)]本文介绍如何使用资源管理器部署模型创建 FQDN。

在 [Azure 预览门户](https://portal.azure.com)中使用“资源管理器”部署模型创建虚拟机时，Azure 预览门户会为虚拟机创建一个公共 IP 资源。可以使用此 IP 地址远程访问虚拟机。但是，默认情况下，Azure 预览门户不会创建[完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)或 FQDN。记住和使用 FQDN 要比记住和使用 IP 地址容易，本文演示了如何将一个 FQDN 添加到你的虚拟机中。

本文假设你已在门户中登录到你的订阅，并使用“资源管理器”创建了一个具有可用映像的虚拟机。虚拟机开始运行后可按照以下步骤操作。

1.  在门户中查看虚拟机设置，然后单击公共 IP 地址。

    ![查找 IP 资源](./media/virtual-machines-create-fqdn-on-portal/locatePublicIP.PNG)

2.  将公共 IP 地址与虚拟机“取消关联”。请注意，它尚未显示域名。单击“是”按钮后，可能需要花费几秒钟时间才能完成取消关联。

    ![取消关联 IP 资源](./media/virtual-machines-create-fqdn-on-portal/dissociateIP.PNG)

    完成以下步骤后，我们会将此公共 IP 地址与虚拟机相关联。如果公共 IP 地址是一个_动态公共 IP 地址_，那么 IPV4 地址将丢失，在配置完 FQDN 之后会分配一个新的 IPV4 地址。

3.  一旦“取消关联”按钮变灰，请单击公共 IP 地址的“所有设置”部分，打开“配置”选项卡。输入所需的 DNS 名称标签。“保存”此配置。

    ![输入 DNS 名称标签](./media/virtual-machines-create-fqdn-on-portal/dnsNameLabel.PNG)

4.  在门户中返回至虚拟机边栏选项卡，然后单击虚拟机的“所有设置”。打开“网络接口”选项卡，然后单击与此虚拟机相关联的网络接口资源。这将在门户中打开“网络接口”边栏选项卡。

    ![打开网络接口](./media/virtual-machines-create-fqdn-on-portal/openNetworkInterface.PNG)

5.  请注意，网络接口的“公共 IP 地址”字段为空。单击此网络接口的“所有设置”部分，打开“IP 地址”选项卡。在“IP 地址”边栏选项卡中，为“公共 IP 地址”字段单击“已启用”。选择“IP 地址配置所需设置”选项卡，然后选择你之前已取消关联的默认 IP。单击“保存”。重新添加 IP 资源可能需要一些时间。

    ![配置 IP 资源](./media/virtual-machines-create-fqdn-on-portal/configureIP.PNG)

6.  关闭所有其他边栏选项卡，然后返回至“虚拟机”边栏选项卡。在设置中单击公共 IP 资源。请注意，公共 IP 边栏选项卡当前将所需 FQDN 显示为“DNS 名称”。

    ![已创建 FQDN](./media/virtual-machines-create-fqdn-on-portal/fqdnCreated.PNG)

    你现在可以使用此 DNS 名称远程连接至虚拟机。例如，连接到完全限定域名为 `testdnslabel.eastus.cloudapp.azure.com` 且用户名为 `adminuser` 的 Linux 虚拟机时，使用 `SSH adminuser@testdnslabel.eastus.cloudapp.azure.com`。

<!---HONumber=79-->