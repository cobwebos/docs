
### <a name="installation-failures"></a>安装失败
| **示例错误消息** | **建议的操作** |
|--------------------------|------------------------|
|错误未能加载帐户。 错误： System.IO.IOException： 无法从传输连接安装和注册 CS 服务器时读取数据。| 确保在计算机上启用了 TLS 1.0。 |

### <a name="registration-failures"></a>注册失败
可以通过查看中的日志调试注册失败**%ProgramData%\ASRLogs**文件夹。

| **示例错误消息** | **建议的操作** |
|--------------------------|------------------------|
|**09:20:06**: InnerException.Type: SrsRestApiClientLib.AcsException,InnerException。<br>消息： ACS50008: SAML 令牌无效。<br>跟踪 ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>相关 ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97 ><br>时间戳： **2016年-12-12 14:50:08Z<br>** | 确保你的系统时钟上的时间不超过 15 分钟关闭本地时间。 重新运行安装程序以完成注册。|
|**09:35:27** ： 为所选证书保管库尝试获取所有灾难恢复时 DRRegistrationException:: 引发 Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException、 Exception.Message: ACS50008: SAML 令牌无效。<br>跟踪 ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>相关 ID: abe9deb8-3e64-464d-8375-36db9816427a<br>时间戳： **2016年-05-19 01:35:39Z**<br> | 确保你的系统时钟上的时间不超过 15 分钟关闭本地时间。 重新运行安装程序以完成注册。|
|06:28:45： 无法创建证书<br>06:28:45:Setup 无法继续。 无法创建对 Site Recovery 进行身份验证所需的证书。 重新运行安装程序 | 确保你以本地管理员身份运行安装程序。 |
