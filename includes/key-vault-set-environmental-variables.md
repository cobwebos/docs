---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013078"
---
应用程序中的 DefaultAzureCredential 方法依赖于三个环境变量：`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET` 和 `AZURE_TENANT_ID`。 请使用 `export VARNAME=VALUE` 格式将这些变量设置为在上述“创建服务主体”步骤中返回的 clientId、clientSecret 和 tenantId 值。 （该方法只为当前 shell 和从 shell 创建的进程设置变量；若要将这些变量永久添加到环境中，请编辑 `/etc/environment ` 文件。） 

还需要将密钥保管库名称另存为名为 `KEY_VAULT_NAME` 的环境变量。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
