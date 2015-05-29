<properties services="virtual-machines" title="Setting up xplat-cli for Resource Manager Templates" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## 使用 xplat-cli

完成以下步骤即可轻松使用包含相应订阅的最新版 xplat-cli。如果你需要安装 xplat-cli 并将其首先连接到你的帐户，请参阅 [Azure 命令行接口 (xplat-cli)](xplat-cli.md)。

## 步骤 1：更新 xplat-cli 版本

若要将 xplat-cli 用于祈使性命令和 ARM 模板，应尽可能安装最新版。若要验证你的版本，请键入 `azure --version`。你应看到类似如下的内容：

    $ azure --version
    0.8.17 (node: 0.10.25)
    
若要更新 xplat-cli 版本，请参阅 [xplat-cli](https://github.com/Azure/azure-xplat-cli)。

## 步骤 2：设置 Azure 帐户和订阅

将 xplat-cli 与要使用的帐户关联后，你可能会发现自己有多个订阅。如果是这种情况，你需要键入以下命令来选择要使用的订阅：

    azure set <subscription id or name> true
    
其中，_subscription id 或 name_ 是你要在当前会话中使用的订阅 ID 或订阅名称。


通过资源管理器模板使用 xplat-cli 并利用资源组部署 Azure 资源和工作负载之前，你需要一个 Azure 帐户（这是当然的）。如果没有帐户，你可以获得[此处的免费 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

如果你没有 Azure 帐户但有 MSDN 订阅，则可以通过激活[此处的 MSDN 订户权益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)获取免费的 Azure 信用额度 -- 也可以使用免费帐户。这两种方式都可以用来进行 Azure 访问。 

<!---HONumber=56-->