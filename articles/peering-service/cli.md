---
title: 使用 Azure CLI 注册对等互连服务预览连接
description: 了解如何使用 Azure CLI 注册对等互连服务连接
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: ef573817927cf732da3426d802f8f26e2e9cd4ec
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398983"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>使用 Azure CLI 注册对等互连服务连接

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 云服务（例如 Microsoft 365、Dynamics 365、软件即服务 (SaaS) services、Azure 或可通过公共 internet 访问的任何 Microsoft 服务）的连接。 本文介绍如何使用 Azure CLI 注册对等互连服务连接。

如果还没有 Azure 订阅，请现在就创建一个[帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，则本文需要 Azure CLI 版本2.0.28 或更高版本。 要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>先决条件 

必须满足以下条件：

### <a name="azure-account"></a>Azure 帐户

必须具有使用中的有效 Microsoft Azure 帐户。 设置对等互连服务连接时需要使用此帐户。 对等互连服务是 Azure 订阅中的一种资源。

### <a name="connectivity-provider"></a>连接服务提供商

可以与 Internet 服务提供商或 Internet 交换合作伙伴合作，以获取对等互连服务，从而将你的网络连接到 Microsoft 网络。

请确保连接服务提供商与 Microsoft 是合作伙伴。

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. 登录到 Azure 帐户并选择订阅

要开始配置，请登录到 Azure 帐户。 如果使用 Cloud Shell **试用** "选项，则会自动登录。 使用以下示例来帮助你进行连接。

```azurecli-interactive
az login
```

检查该帐户的订阅。

```azurecli-interactive
az account list
```

选择要为其注册对等互连服务连接的订阅。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

如果还没有资源组，则必须先创建一个资源组，然后才能注册对等连接服务连接。 可以运行以下命令来创建资源组：

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. 将订阅注册到资源提供程序和功能标志

在继续执行使用 Azure CLI 注册对等互连服务连接的步骤之前，请使用 Azure CLI 将订阅注册到资源提供程序和功能标志。 Azure CLI 命令在此处指定：

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. 注册对等互连服务连接

通过使用以下命令集注册对等互连服务连接： Azure CLI。 此示例注册名为 myPeeringService 的对等互连服务连接。

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. 注册前缀

通过执行以下命令，通过 Azure CLI 来注册连接提供程序提供的前缀。 此示例注册名为 myPrefix 的前缀。

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>后续步骤

- 若要了解有关对等互连服务连接的详细信息，请参阅对 [等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 注册连接，请参阅[注册对等互连服务连接 - Azure PowerShell](powershell.md)。
- 若要使用 Azure 门户注册连接，请参阅 [注册对等互连服务连接-Azure 门户](azure-portal.md)。
