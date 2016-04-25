<properties
   pageTitle="用于管理应用程序的文档库 | Microsoft Azure"
   description="有关 Azure Active Directory 应用程序管理的主题，提供操作说明、故障排除和常见问题解答的参考链接"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.date="12/03/2015"
   wacn.date=""/>

# 用于管理应用程序的文档库

本页包含通过 Azure Active Directory 实现单一登录的主要功能的有用链接。链接已组织成以下类别：

- **概述：**功能概述，包括用例和主题简介。
- **操作说明：**帮助你开始使用特定方案的详细说明。
- **故障排除：**帮助你诊断常见问题的提示。
- **常见问题：**常见问题和疑虑的解答。  

## Cloud App Discovery

| | |
| ------ | ------ |
| 概述 | [如何发现本组织中使用的未经认可的云应用](/documentation/articles/active-directory-cloudappdiscovery-whatis) |
| 操作说明 | [Cloud App Discovery 入门](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)<br><br>[Cloud App Discovery 安全与隐私注意事项](/documentation/articles/active-directory-cloudappdiscovery-security-and-privacy-considerations)<br><br>[Cloud App Discovery 组策略部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br><br>[Cloud App Discovery System Center 部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br><br>[代理服务的 Cloud App Discovery 注册表设置](/documentation/articles/active-directory-cloudappdiscovery-registry-settings-for-proxy-services) |
| 常见问题 | [Cloud App Discovery — 常见问题](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## 应用分配

| | |
| ------ | ------ |
| 概述 | [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups) |
| 操作说明 | [创建简单规则以配置组的动态成员身份](/documentation/articles/active-directory-accessmanagement-simplerulegroup) <br><br> [使用组来管理对 SaaS 应用程序的访问](/documentation/articles/active-directory-accessmanagement-group-saasapps) <br><br> [为自助应用程序访问管理设置 Azure AD](/documentation/articles/active-directory-accessmanagement-self-service-group-management) <br><br> [管理组的所有者](/documentation/articles/active-directory-accessmanagement-managing-group-owners) <br><br> [管理 Azure Active Directory 中的安全组](/documentation/articles/active-directory-accessmanagement-manage-groups) <br><br> [Azure Active Directory 中的专用组](/documentation/articles/active-directory-accessmanagement-dedicated-groups) <br><br> [使用属性创建高级规则](/documentation/articles/active-directory-accessmanagement-groups-with-advanced-rules) <br><br> [使用 Azure Active Directory 自动预配和取消预配 SaaS 应用程序的用户](/documentation/articles/active-directory-saas-app-provisioning) <br><br> [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](/documentation/articles/active-directory-saas-tutorial-list) |

## 联合单一登录

| | |
| ------ | ------ |
| 概述 |[配置与 AD FS 的联合](/documentation/articles/active-directory-aadconnect-get-started-custom)
| 操作说明 | [DirSync 与单一登录](https://msdn.microsoft.com/library/azure/dn441213.aspx)<br><br>[Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/)<br><br>[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)<br><br>[Active Directory 应用商店](https://azure.microsoft.com/marketplace/active-directory/) |
| 故障排除 | [Azure AD DirSync 工具配置向导故障排除](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx)<br><br>[如何排查 Azure Active Directory 同步工具安装和配置向导错误](https://support.microsoft.com/kb/2684395)<br><br>[目录同步故障排除](https://msdn.microsoft.com/library/azure/jj151787.aspx)<br><br>[单一登录故障排除](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| 常见问题 | [在 Azure Active Directory 中管理联合单一登录的证书](/documentation/articles/active-directory-sso-certs)<br><br>[Azure Active Directory Connect 常见问题](/documentation/articles/active-directory-aadconnect-faq) |

## 密码单一登录

| | |
| ------ | ------ |
| 操作说明 | [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)<br><br>[具有密码同步功能的 DirSync](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| 故障排除 | [Azure AD DirSync 工具配置向导故障排除](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx)<br><br>[如何排查 Azure Active Directory 同步工具安装和配置向导错误](https://support.microsoft.com/kb/2684395)<br><br>[目录同步故障排除](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| 常见问题 | [Azure Active Directory Connect 常见问题](active-directory-aadconnect-faq.md) |

## 富应用预配

| | |
| ------ | ------ |
| 概述 | [使用 Azure Active Directory 自动预配和取消预配 SaaS 应用程序的用户](/documentation/articles/active-directory-saas-app-provisioning) |
| 操作说明 | [教程：Azure Active Directory 与 Concur 的集成](/documentation/articles/active-directory-saas-concur-tutorial)<br><br>[教程：Azure Active Directory 与 DocuSign 的集成](active-directory-saas-docussign-tutorial.md)<br><br>[教程：Azure Active Directory 与 Dropbox for Business 的集成](/documentation/articles/active-directory-saas-dropboxforbusiness-tutorial)<br><br>[教程：如何将 Google Apps 与 Azure Active Directory 集成](active-directory-saas-google-apps-tutorial.md)<br><br>[教程：如何将 Salesforce 与 Azure Active Directory 集成](active-directory-saas-salesforce-tutorial.md)<br><br>[教程：Azure Active Directory 与 ServiceNow 的集成](active-directory-saas-servicenow-tutorial.md)<br><br>[教程：配置 Workday 以实现入站同步](active-directory-saas-workday-inbound-tutorial.md) |

## Azure AD 应用库

| | |
| ------ | ------ |
| 操作说明 | [列出 Azure Active Directory 应用程序库中的应用程序](/documentation/articles/active-directory-app-gallery-listing)<br><br>[使用 Azure AD 自助 SAML 配置加入自己的应用](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## 自定义应用

| | |
| ------ | ------ |
| 概述 | [使用 Azure AD 自助 SAML 配置加入自己的应用](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| 操作说明 | [教程：如何将 Salesforce 与 Azure Active Directory 集成](/documentation/articles/active-directory-saas-salesforce-tutorial) |

## 应用代理

| | |
| ------ | ------ |
| 概述 | [如何提供对本地应用程序的安全远程访问](/documentation/articles/active-directory-application-proxy-get-started) |
| 操作说明 | [启用 Azure AD 应用程序代理](/documentation/articles/active-directory-application-proxy-enable)<br><br>[使用 Azure AD 应用程序代理发布应用程序](/documentation/articles/active-directory-application-proxy-publish)<br><br>[使用条件性访问](/documentation/articles/active-directory-application-proxy-conditional-access)<br><br>[在 Azure AD 应用程序代理中使用自定义域](/documentation/articles/active-directory-application-proxy-custom-domains)<br><br>[在应用程序代理中使用声明感知应用](active-directory-application-proxy-claims-aware-apps.md)<br><br>[使用应用程序代理进行单一登录](/documentation/articles/active-directory-application-proxy-sso-using-kcd) |
| 故障排除 | [应用程序代理故障排除](/documentation/articles/active-directory-application-proxy-troubleshoot) |

## 应用启动体验

| | |
| ------ | ------ |
| 概述 | [访问面板简介](/documentation/articles/active-directory-saas-access-panel-introduction) |
| 操作说明 | [Azure Active Directory 访问面板 - EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx)<br><br>[与 Azure Active Directory 集成](/documentation/articles/active-directory-how-to-integrate) |

<!---HONumber=Mooncake_0418_2016-->