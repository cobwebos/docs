---
title: 在 Azure 上创建 Pivotal Cloud Foundry 群集
description: 了解如何设置所需的参数，以便在 Azure 上预配 Pivotal 群集
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 11c8fdca595840389f318ce9810674d53ac7f2d2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273364"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>在 Azure 上创建 Pivotal Cloud Foundry 群集

本教程提供的快速步骤用于创建和生成在 Azure 上预配 Pivotal Cloud Foundry (PCF) 群集所需的参数。 若要查找 Pivotal Cloud Foundry 解决方案，请在 Azure [市场](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)中执行搜索。

![在 Azure 中搜索 Pivotal Cloud Foundry](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>生成 SSH 公钥

可在 Windows、Mac 或 Linux 中使用多种方式生成安全外壳 (SSH) 公钥。

```Bash
ssh-keygen -t rsa -b 2048
```

有关详细信息，请参阅[在 Azure 上将 SSH 密钥与 Windows 配合使用](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="create-a-service-principal"></a>创建服务主体

> [!NOTE]
>
> 若要创建服务主体，需要所有者帐户权限。 还可以编写一个脚本来自动创建服务主体。 例如，可以使用 Azure CLI 的 [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)。

1. 登录到 Azure 帐户。

    `az login`

    ![Azure CLI 登录](media/deploy/az-login-output.png )
 
    复制“id”值（用作**订阅 ID**）和“tenantId”值供以后使用。

2. 设置此配置的默认订阅。

    `az account set -s {id}`

3. 创建适用于 PCF 的 Azure Active Directory 应用程序。 指定唯一的字母数字密码。 将密码存储为 **clientSecret**，供以后使用。

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    复制输出中的“appId”值（用作 **ClientID**），供以后使用。

    > [!NOTE]
    >
    > 选择自己的应用程序主页和标识符 URI，例如 [http://www.contoso.com](http://www.contoso.com)。

4. 使用新的应用 ID 创建服务主体。

    `az ad sp create --id {appId}`

5. 将服务主体的权限角色设置为“参与者”。

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor”`

    也可使用

    `az role assignment create --assignee {service-principal-name} --role “Contributor”`

    ![服务主体角色分配](media/deploy/svc-princ.png )

6. 使用应用 ID、密码和租户 ID 验证是否可以成功登录到自己的服务主体。

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. 使用以下格式创建 .json 文件。 使用此前复制的**订阅 ID**、**tenantID**、**clientID** 和 **clientSecret** 的值。 保存文件。

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>获取 Pivotal Network 令牌

1. 注册或登录到 [Pivotal Network](https://network.pivotal.io) 帐户。
2. 在页面右上角选择配置文件名称。 选择“编辑配置文件”。 
3. 滚动到页面底部并复制“旧 API 令牌”值。  此值是稍后要使用的“Pivotal Network 令牌”值。 

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>在 Azure 上预配 Cloud Foundry 群集

现在已经有了预配 [Azure 上的 Pivotal Cloud Foundry群集](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)所需的所有参数。
输入参数并创建 PCF 群集。

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>验证部署并登录到 Pivotal Ops Manager

1. PCF 群集会显示部署状态。

    ![Azure 部署状态](media/deploy/deployment.png )

2. 在左侧导航中选择“部署”链接，以便获取 PCF Ops Manager 的凭据。  在下一页中选择“部署名称”。 
3. 在左侧导航中选择“输出”链接，以便显示 PCF Ops Manager 的 URL、用户名和密码。  “OPSMAN-FQDN”值是 URL。
 
    ![Cloud Foundry 部署输出](media/deploy/deploy-outputs.png )
 
4. 在 Web 浏览器中启动 URL。 输入在上一步获取的登录凭据。

    ![Pivotal 登录页](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > 如果 Internet Explorer 浏览器因出现“站点不安全”警告消息而发生故障，请选择“详细信息”，然后转到相关网页。  对于 Firefox，请选择“继续”，然后添加证书以继续。 

5. PCF Ops Manager 会显示已部署的 Azure 实例。 现在可在此处部署并管理应用程序。
               
    ![在 Pivotal 中部署的 Azure 实例](media/deploy/ops-mgr.png )
 
