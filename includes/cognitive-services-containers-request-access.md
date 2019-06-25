---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: 4cdcec850f32d7e94f33eb28e5bf7839e511f347
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173273"
---
填写并提交[认知服务影像容器请求窗体](https://aka.ms/VisionContainersPreview)容器请求访问。 通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 在提交表单后，Azure 认知服务团队会检查，以确保满足的条件访问专用容器注册表的权限。

> [!IMPORTANT]
> 必须使用与 Microsoft 帐户 (MSA) 或窗体中的 Azure Active Directory (Azure AD) 帐户相关联的电子邮件地址。

如果你的请求得到批准，您将收到一封电子邮件的说明介绍如何获取你的凭据和访问专用容器注册表的。

## <a name="log-in-to-the-private-container-registry"></a>登录到专用容器注册表

有几种方法使用认知服务容器的专用容器注册表进行身份验证。 我们建议你通过使用命令行方法[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)。

使用[docker 登录名](https://docs.docker.com/engine/reference/commandline/login/)命令，如下面的示例中所示中登录到`containerpreview.azurecr.io`，这是专用容器注册表的认知服务容器。 将 \<username\> 替换为用户名，将 \<password\> 替换为从 Azure 认知服务团队收到的凭据中提供的密码   。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果你保护你的凭据在文本文件中，您可以连接到该文本文件的内容`docker login`命令。 使用`cat`命令，如下面的示例中所示。 替换 *\<passwordFile\>* 使用的路径和包含密码的文本文件的名称。 替换 *\<用户名\>* 与你的凭据中提供的用户名称。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

