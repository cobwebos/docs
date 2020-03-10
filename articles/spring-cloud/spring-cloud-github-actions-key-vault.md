---
title: 在 GitHub 操作中对 Azure 春季 Cloud 进行 Key Vault 身份验证
description: 如何使用带有 GitHub 操作的 Azure 春季 Cloud 的 CI/CD 工作流的密钥保管库
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945474"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>在 GitHub 操作中对 Azure 春季 Cloud 进行 Key Vault 身份验证
Key vault 是存储密钥的安全位置。 企业用户需要在其控制范围内的 CI/CD 环境中存储凭据。 用于获取密钥保管库中凭据的密钥应限制为资源作用域。  它仅可以访问密钥保管库作用域，而不能访问整个 Azure 作用域。 它类似于只能打开一个强框的键，而不是可打开大楼中所有门的主密钥。 这是一种使用另一个密钥获取密钥的方法，这在 CICD 工作流中非常有用。 

## <a name="generate-credential"></a>生成凭据
若要生成密钥以访问密钥保管库，请在本地计算机上执行以下命令：
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
由 `--scopes` 参数指定的范围限制对资源的密钥访问。  它只能访问强框。

具有结果：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
然后，将结果保存到 GitHub**机密**，如[设置 github 存储库和通过 Azure 进行身份验证](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)中所述。

## <a name="add-access-policies-for-the-credential"></a>为凭据添加访问策略
上面创建的凭据只能获取有关 Key Vault 的常规信息，而不能获取其存储的内容。  若要获取 Key Vault 中存储的机密，需要为凭据设置访问策略。

中转到 Azure 门户中的**Key Vault**仪表板，单击 "**访问控制**" 菜单，并打开 "**角色分配**" 选项卡。为 "**类型**" 选择 "**应用**"，并为 "**作用域**" `This resource`。  你应看到在上一步中创建的凭据：

 ![设置访问策略](./media/github-actions/key-vault1.png)

复制凭据名称，例如 `azure-cli-2020-01-19-04-39-02`。 打开 "**访问策略**" 菜单，单击 " **+ 添加访问策略**链接"。  选择 "`Secret Management` 作为**模板**"，并选择 "**主体**"。 将凭据名称粘贴到**主体**/**选择**输入框：

 ![Select](./media/github-actions/key-vault2.png)

 单击 "**添加访问策略**" 对话框中的 "**添加**" 按钮，然后单击 "**保存**"。

## <a name="generate-full-scope-azure-credential"></a>生成完全范围的 Azure 凭据
这是打开大楼中所有门的主密钥。 此过程类似于上一步，但此处我们更改了作用域，以生成主密钥：

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

同样，结果如下：
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
复制整个 JSON 字符串。  向后**Key Vault**仪表板。 打开 "**机密**" 菜单，然后单击 "**生成/导入**" 按钮。 输入机密名称，如 `AZURE-CRENDENTIALS-FOR-SPRING`。 将 JSON 凭据字符串粘贴到 "**值**" 输入框。 你可能会注意到，值输入框是一个单行文本字段，而不是多行文本区域。  可以在其中粘贴完整的 JSON 字符串。

 ![完整作用域凭据](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>在 GitHub 操作中合并凭据
设置 CICD 管道执行时使用的凭据：

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>后续步骤
* [春季云 GitHub 操作](./spring-cloud-howto-github-actions.md)