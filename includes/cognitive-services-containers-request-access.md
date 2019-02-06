---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 08e6b5d109d6647f2a291f117f4993bae7598464
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302274"
---
在请求访问容器之前，必须先填写并提交[认知服务视觉容器请求表单](https://aka.ms/VisionContainersPreview)。 通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交后，Azure 认知服务团队可以检查表单，以确保满足访问专用容器注册表的条件。

> [!IMPORTANT]
> 必须使用与表单中的 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 帐户关联的电子邮件地址。

如果请求获得批准，则你会收到一封电子邮件，其中说明了如何获取凭据和访问专用容器注册表。

## <a name="log-in-to-the-private-container-registry"></a>登录到专用容器注册表

可通过多种方法使用认知服务容器的专用容器注册表进行身份验证，但建议的方法是在 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) 中使用命令行。

使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令（如以下示例所示）登录到 `containerpreview.azurecr.io`，即认知服务容器的专用容器注册表。 将 \<username\> 替换为用户名，将 \<password\> 替换为从 Azure 认知服务团队收到的凭据中提供的密码。

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果已在文本文件中保护了凭据，则可以使用 `cat` 命令将该文本文件的内容连接到 `docker login` 命令，如以下示例所示。 将 \<passwordFile\> 替换为包含密码的文本文件的路径和名称，将 \<username\> 替换为凭据中提供的用户名。

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

