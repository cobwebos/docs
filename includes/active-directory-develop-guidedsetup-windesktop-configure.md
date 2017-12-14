
## <a name="create-an-application-express"></a>创建应用程序（快速）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)注册应用程序
2.  输入应用程序的名称和电子邮件
3.  确保选中“指导式设置”选项
4.  按照说明获取应用程序 ID，并将其粘贴到代码中

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>将应用程序注册信息添加到解决方案（高级）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：
1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和电子邮件 
3. 确保取消选中“指导式设置”选项
4. 单击`Add Platform`，选择`Native Application`并单击“保存”
5. 复制应用程序 ID 中的 GUID，返回 Visual Studio，打开 `App.xaml.cs`，并用刚才注册的应用程序 ID 替换 `your_client_id_here`：

```csharp
private static string ClientId = "your_application_id_here";
```
