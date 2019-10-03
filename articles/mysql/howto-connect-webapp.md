---
title: 将现有的 Azure 应用服务连接到 Azure Database for MySQL
description: 有关如何正确地将现有的 Azure 应用服务连接到 Azure Database for MySQL 的说明
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/21/2019
ms.openlocfilehash: 3fbffc805afb540499e38f1c0853260968228b22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002010"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>将现有的 Azure 应用服务连接到 Azure Database for MySQL 服务器
本主题将说明如何将现有的 Azure App Service 连接到 Azure Database for MySQL 服务器。

## <a name="before-you-begin"></a>开始之前
登录到 [Azure 门户](https://portal.azure.com)。 创建 Azure Database for MySQL 服务器。 有关详细信息，请参阅[如何在门户中创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。

当前有两种解决方案，都允许从 Azure 应用服务访问 Azure Database for MySQL。 这两种解决方案都涉及设置服务器级别的防火墙规则。

## <a name="solution-1---allow-azure-services"></a>解决方案 1 - 允许 Azure 服务
Azure Database for MySQL 通过使用防火墙保护数据来确保访问安全。 从 Azure App Service 连接到 Azure Database for MySQL 服务器时，请记住应用服务的出站 IP 实际上为动态 IP。 选择“允许访问 Azure 服务”选项将允许应用服务连接到 MySQL 服务器。

1. 在 MySQL 服务器边栏选项卡上的“设置”标题下，单击“连接安全性”，以打开 Azure Database for MySQL 的“连接安全性”边栏选项卡  。

   ![Azure 门户 - 单击连接安全性](./media/howto-connect-webapp/1-connection-security.png)

2. 在“允许访问 Azure 服务”  中选择“开”  ，然后选择“保存”  。
   ![Azure 门户 - 允许 Azure 访问](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>解决方案 2 - 创建显式允许出站 IP 的防火墙规则
可显式添加 Azure 应用服务的所有出站 IP。

1. 在应用服务的“属性”边栏选项卡中，查看“出站 IP 地址”  。

   ![Azure 门户 - 查看出站 IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. 在 MySQL 的“连接安全性”边栏选项卡中，逐个添加出站 IP。

   ![Azure 门户 - 添加显式 IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. 请记住保存  防火墙规则。

尽管 Azure App Service 会尝试在一段时间内将 IP 地址保持不变，但也有 IP 地址可能发生变动的情况。 例如，当应用回收、出现缩放操作或在 Azure 区域数据中心添加新计算机以增加容量时，IP 地址可能发生变动。 IP 地址变动时，应用若无法再连接到 MySQL 服务器，则可能会停机。 选择上述任何一种解决方案时，请考虑此可能性。

## <a name="ssl-configuration"></a>SSL 配置
Azure Database for MySQL 已默认启用 SSL。 如果应用程序不使用 SSL 连接到数据库，则需禁用 MySQL 服务器上的 SSL。 有关如何配置 SSL 的详细信息，请参阅[通过 Azure Database for MySQL 使用 SSL](howto-configure-ssl.md)。

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
有关连接字符串的详细信息，请参阅[连接字符串](howto-connection-string.md)。
