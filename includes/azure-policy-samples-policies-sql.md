---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 7a5d4f7ee4bd5c9377fb53c18a7f07c9d6f8aa3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170315"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmails_Audit.json) |确保为“高级数据安全性”服务器设置中的“将警报发送到”字段提供电子邮件地址。 在 SQL 托管实例上检测到异常活动时，此电子邮件地址将会收到警报通知。 |AuditIfNotExists、Disabled |1.0.0 |
|[SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmails_Audit.json) |确保为“高级数据安全性”服务器设置中的“将警报发送到”字段提供电子邮件地址。 在 SQL 服务器上检测到异常活动时，此电子邮件地址将会收到警报通知。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 托管实例上启用高级数据安全性](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |审核没有高级数据安全的 SQL 托管实例 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 服务器上启用高级数据安全性](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |审核没有高级数据安全的 SQL 服务器 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureThreatDetectionTypes_Audit.json) |建议在 SQL 服务器上启用所有高级威胁防护类型。 启用所有类型可以防范 SQL 注入、数据库漏洞和任何其他异常活动。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_EnsureThreatDetectionTypes_Audit.json) |建议在 SQL 服务器上启用所有高级威胁防护类型。 启用所有类型可以防范 SQL 注入、数据库漏洞和任何其他异常活动。 |AuditIfNotExists、Disabled |1.0.0 |
|[应该为 SQL 服务器预配 Azure Active Directory 管理员](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |审核确认已为 SQL Server 预配了 Azure Active Directory 管理员以启用 Azure AD 身份验证。 使用 Azure AD 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理 |AuditIfNotExists、Disabled |1.0.0 |
|[应启用 SQL 服务器上的审核](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |应在 SQL 服务器上启用审核以跟踪服务器上所有数据库的数据库活动，并将其保存在审核日志中。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为 PostgreSQL 数据库服务器启用连接限制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |此策略帮助审核环境中任何未启用连接限制的 PostgreSQL 数据库。 无效密码登录失败次数过多时，可以使用此设置来按 IP 限制临时连接。 |AuditIfNotExists、Disabled |1.0.0 |
|[在 SQL 服务器上部署高级数据安全](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAdvancedDataSecurity_Deploy.json) |此策略在 SQL 服务器上启用高级数据安全性。 这包括启用威胁检测和漏洞评估。 它自动在 SQL 服务器所在的同一区域和资源组中，创建一个带有“sqlva”前缀存储帐户用于存储扫描结果。 |DeployIfNotExists |1.0.0 |
|[对 SQL 服务器部署审核](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_Deploy.json) |此策略确保在 SQL 服务器上启用审核，以增强安全性与合规性。 它自动在 SQL 服务器所在的同一区域中创建一个存储帐户用于存储审核记录。 |DeployIfNotExists |1.0.0 |
|[将 Azure SQL 数据库的诊断设置部署到事件中心](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDB_DiagnosticsLog_Deploy.json) |在创建或更新缺少 Azure SQL 数据库的诊断设置的 Azure SQL 数据库时，将此诊断设置流式部署到区域事件中心。 |DeployIfNotExists |1.0.0 |
|[部署 SQL DB 透明数据加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDBEncryption_Deploy.json) |在 SQL 数据库上启用透明数据加密 |DeployIfNotExists |1.0.0 |
|[在 SQL 服务器上部署威胁检测](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/DeployTdOnSqlServers_Deploy.json) |此策略可确保在 SQL 服务器上启用威胁检测。 |DeployIfNotExists |1.0.0 |
|[应为 PostgreSQL 数据库服务器记录断开连接](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |此策略帮助审核环境中任何未启用 log_disconnections 的 PostgreSQL 数据库。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurityEmailAdmins_Audit.json) |审核是否已在 SQL 托管实例高级威胁防护设置中启用“向管理员和订阅所有者发送电子邮件通知”。 这可以确保尽快向管理员报告在 SQL 托管实例上检测到的任何异常活动。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurityEmailAdmins_Audit.json) |审核是否已在 SQL 服务器高级威胁防护设置中启用“向管理员和订阅所有者发送电子邮件通知”。 这可以确保尽快向管理员报告在 SQL 服务器上检测到的任何异常活动。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为 MySQL 数据库服务器启用“强制 SSL 连接”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |此策略审核不强制 SSL 连接的任何 MySQL 服务器。 Azure Database for MySQL 支持使用安全套接字层 (SSL) 将 Azure Database for MySQL 服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。 |Audit、Disabled |1.0.0 |
|[应为 PostgreSQL 数据库服务器启用“强制 SSL 连接”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |此策略审核不强制 SSL 连接的任何 PostgreSQL 服务器。 Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击 |Audit、Disabled |1.0.0 |
|[应为 Azure Database for MariaDB 启用异地冗余备份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |此策略将审核未启用异地冗余备份的任何 Azure Database for MariaDB。 |Audit、Disabled |1.0.0 |
|[应为 Azure Database for MySQL 启用异地冗余备份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |此策略将审核未启用异地冗余备份的任何 Azure Database for MySQL。 |Audit、Disabled |1.0.0 |
|[应为 Azure Database for PostgreSQL 启用异地冗余备份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |此策略将审核未启用异地冗余备份的任何 Azure Database for PostgreSQL。 |Audit、Disabled |1.0.0 |
|[应为 PostgreSQL 数据库服务器启用“记录检查点”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |此策略帮助审核环境中任何未启用 log_checkpoints 设置的 PostgreSQL 数据库。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为 PostgreSQL 数据库服务器启用“记录连接”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |此策略帮助审核环境中任何未启用 log_connections 设置的 PostgreSQL 数据库。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为 PostgreSQL 数据库服务器启用“记录持续时间”](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDuration_Audit.json) |此策略帮助审核环境中任何未启用 log_duration 设置的 PostgreSQL 数据库。 |AuditIfNotExists、Disabled |1.0.0 |
|[应为 Azure SQL 数据库启用长期异地冗余备份](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |此策略将审核未启用长期异地冗余备份的任何 Azure SQL 数据库。 |AuditIfNotExists、Disabled |1.0.0 |
|[SQL 审核设置中应包含配置为捕获关键活动的操作组](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |AuditActionsAndGroups 属性应至少包含 SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP 以确保全面审核日志记录 |AuditIfNotExists、Disabled |1.0.0 |
|[应使用自己的密钥加密 SQL 托管实例的 TDE 保护器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |使用你自己的密钥支持的透明数据加密(TDE)增加了透明度和对 TDE 保护器的控制，增强了由 HSM 提供支持的外部服务的安全性，并促进了职责划分。 |AuditIfNotExists、Disabled |1.0.0 |
|[应使用自己的密钥加密 SQL 服务器的 TDE 保护器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |使用你自己的密钥支持的透明数据加密(TDE)增加了透明度和对 TDE 保护器的控制，增强了由 HSM 提供支持的外部服务的安全性，并促进了职责划分。 |AuditIfNotExists、Disabled |1.0.0 |
|[应将 SQL 服务器的审核保留期配置为大于 90 天](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |审核配置的审核保持期少于 90 天的 SQL 服务器。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 SQL 数据库上启用透明数据加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |应启用透明数据加密以保护静态数据并满足符合性要求 |AuditIfNotExists、Disabled |1.0.0 |
|[SQL 服务器的漏洞评估设置应包含用来接收扫描报告的电子邮件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |确保为漏洞评估设置中的“将扫描报告发送到”字段提供电子邮件地址。 在 SQL 服务器上运行定期扫描后，此电子邮件地址将收到扫描结果摘要。 |AuditIfNotExists、Disabled |1.0.0 |
|[应对 SQL 托管实例启用漏洞评估](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |审核未启用定期漏洞评估扫描的 SQL 托管实例。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |1.0.0 |
|[应对 SQL 服务器启用漏洞评估](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |审核未启用定期漏洞评估扫描的 Azure SQL 服务器。 漏洞评估可发现、跟踪和帮助你修正潜在数据库漏洞。 |AuditIfNotExists、Disabled |1.0.0 |
