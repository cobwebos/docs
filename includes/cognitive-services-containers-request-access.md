---
author: IEvangelist
ms.author: dapine
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.openlocfilehash: 993b0e8cc5b1ec482b2f6041dfc970dc7e7409dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68229302"
---
填写并提交[认知服务视觉容器请求表单](https://aka.ms/VisionContainersPreview)以请求访问容器。 通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交表单后，Azure 认知服务团队会审核它，以确保满足访问专用容器注册表的条件。

> [!IMPORTANT]
> 您必须使用与窗体中的 Microsoft 帐户 （MSA） 或 Azure 活动目录 （Azure AD） 帐户关联的电子邮件地址。

如果您的请求获得批准，您将收到一封电子邮件，其中包含说明如何获取凭据和访问专用容器注册表的说明。

## <a name="log-in-to-the-private-container-registry"></a>登录到专用容器注册表

有几种方法可以使用认知服务容器的专用容器注册表进行身份验证。 我们建议您使用[Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)使用命令行方法。

使用[docker 登录](https://docs.docker.com/engine/reference/commandline/login/)命令（如以下示例所示）登录到`containerpreview.azurecr.io`，这是认知服务容器的专用容器注册表。 将*\<用户名\>* 替换为*\<用户名和密码\>，以及*从 Azure 认知服务团队收到的凭据中提供的密码。

```
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果在文本文件中保护凭据，则可以将该文本文件的内容串联到命令中`docker login`。 使用`cat`命令，如以下示例所示。 将*\<密码文件\>* 替换为包含密码的文本文件的路径和名称。 将*\<用户名\>* 替换为凭据中提供的用户名。

```
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

