---
title: 在 Azure 上创建 Cloud Foundry
description: 了解如何设置所需的参数，以便在 Azure 上预配 Cloud Foundry PCF 群集
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250547"
---
# <a name="create-cloud-foundry-on-azure"></a>在 Azure 上创建 Cloud Foundry

本教程提供有关创建和生成所需的参数，以便在 Azure 上预配 Pivotal Cloud Foundry PCF 群集的快速步骤。  可以通过在 Azure [市场](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)中执行搜索，来查找 Pivotal Cloud Foundry 解决方案。

![替代图像文本](media/deploy/pcf-marketplace.png "在 Azure 中搜索 Pivotal Cloud Foundry")


## <a name="generate-an-ssh-public-key"></a>生成 SSH 公钥

可在 Windows、Mac 或 Linux 中使用多种方式生成 SSH 公钥。

```Bash
ssh-keygen -t rsa -b 2048
```
- 请单击此处查看适用于你的环境的[说明]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

## <a name="create-a-service-principal"></a>创建服务主体

> [!NOTE]
>
> 创建服务主体需要所有者帐户权限。  此外，可以编写一个脚本来自动创建服务主体。 例如，使用 Azure CLI [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)。

1. 登录到 Azure 帐户。

    `az login`

    ![替代图像文本](media/deploy/az-login-output.png "Azure CLI 登录")
 
    复制“id”值（用作**订阅 ID**）和 **tenantId** 值供稍后使用。

2. 设置此配置的默认订阅。

    `az account set -s {id}`

3. 为 PCF 创建 AAD 应用程序，并指定唯一的字母数字密码。  将密码存储为 **clientSecret**，供稍后使用。

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    复制输出中的“appId”（用作 **ClientID**），供稍后使用。

    > [!NOTE]
    >
    > 选择自己的应用程序主页和标识符 URI。  例如， http://www.contoso.com

4. 使用新的“appId”创建服务主体。

    `az ad sp create --id {appId}`

5. 将服务主体的权限角色设置为“参与者”。

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    也可以使用...

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![替代图像文本](media/deploy/svc-princ.png "服务主体角色分配")

6. 使用 appId、password 和 tenantId 验证是否可以成功登录到自己的服务主体。

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. 使用前面复制的所有**订阅 ID**、**tenantId**、**clientID** 和 **clientSecret** 值创建采用以下格式的 .json 文件。  保存文件。

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
2. 在页面右上角单击自己的个人资料名称，然后选择“编辑个人资料”。
3. 滚动到页面底部并复制“旧 API 令牌”值。  这是稍后要使用的“Pivotal Network 令牌”值。

## <a name="provision-your-cloud-foundry-on-azure"></a>在 Azure 上预配 Cloud Foundry

1. 现已设置所需的全部参数，接下来可在 Azure 群集上预配 [Pivotal Cloud Foundry](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)。
2. 输入参数并创建 PCF 群集。

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>验证部署并登录到 Pivotal Ops Manager

1. PCF 群集应显示部署状态。

    ![替代图像文本](media/deploy/deployment.png "Azure 部署状态")

2. 在左侧导航栏中，单击“部署”链接以便在 PCF Ops Manager 中获取凭据，然后在下一页中单击“部署名称”。
3. 在左侧导航栏中，单击“输出”链接以便在 PCF Ops Manager 中显示“URL”、“用户名”和“密码”。  “OPSMAN-FQDN”值是 URL。
 
    ![替代图像文本](media/deploy/deploy-outputs.png "Cloud Foundry 部署输出")
 
4. 在 Web 浏览器中启动该 URL，并输入上一步骤中获取的凭据以登录。

    ![替代图像文本](media/deploy/pivotal-login.png "Pivotal 登录页")
         
    > [!NOTE]
    >
    > 如果 Internet Explorer 浏览器因出现站点不安全警告消息而失败，请单击“详细信息”和“继续转到网页”。  对于 Firefox，请单击“继续”并添加证书以继续。

5. PCF Ops Manager 应显示已部署的 Azure 实例。 现在，可在此处开始部署和管理应用程序！
               
    ![替代图像文本](media/deploy/ops-mgr.png "Pivotal 中已部署的 Azure 实例")
 
