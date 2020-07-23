---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.openlocfilehash: 66bd78c94e6c54d26959778cc059730c13d02629
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698564"
---
通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交表单后，Azure 认知服务团队会对其进行检查，以确保满足访问专用容器注册表的条件。

> [!IMPORTANT]
> 必须使用与 Microsoft 帐户（MSA）或 Azure Active Directory （Azure AD）帐户关联的电子邮件地址。

如果你的请求已获批准，则会收到一封电子邮件，其中包含描述如何获取凭据和访问专用容器注册表的说明。

## <a name="log-in-to-the-private-container-registry"></a>登录到专用容器注册表

有多种方法可通过用于认知服务容器的私有容器注册表进行身份验证。 建议使用[DOCKER CLI](https://docs.docker.com/engine/reference/commandline/cli/)来使用命令行方法。

如以下示例中所示，使用[docker login](https://docs.docker.com/engine/reference/commandline/login/)命令登录到 `containerpreview.azurecr.io` ，这是认知服务容器的专用容器注册表。 将* \< username \> *替换为用户名和* \< 密码 \> * ，将密码替换为你在 Azure 认知服务团队收到的凭据中提供的密码。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果在文本文件中保护凭据，则可以将该文本文件的内容连接到 `docker login` 命令。 使用 `cat` 命令，如以下示例中所示。 将* \< passwordFile \> *替换为包含密码的文本文件的路径和名称。 将* \< username \> *替换为凭据中提供的用户名。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

