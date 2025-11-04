# WebExtensions

WebExtensions 是一个轻量级的 .NET 工具库，提供 HTTP 请求处理、Cookie 管理、JSON 解析、实用工具功能和加密解密功能，专为 .NET 应用程序的网络扩展和开发需求设计。

## 主要功能

### 1. HTTP 请求处理
- 支持 GET、POST 等多种 HTTP 方法
- 文件上传功能
- 自定义请求头管理（包括临时请求头）
- 代理设置支持
- SSL 证书验证选项
- Cookie 管理集成

### 2. Cookie 管理器
- 灵活的 Cookie 设置和获取
- 从 CoreWebView2CookieManager 导入/导出 Cookie
- 批量 Cookie 操作
- Cookie 字符串解析和处理
- Cookie 存在性检查

### 3. JSON 处理
- JSON 字符串验证
- 动态对象解析
- JSON 序列化和反序列化
- 数组和对象解析

### 4. 实用工具功能
- URL 参数排序（按 ASCII 码顺序）
- 查询字符串转换为字典
- 字典转换为查询字符串
- URL 全路径参数排序
- 随机码生成
- 时间戳获取
- URL 参数解析（支持多个等号的情况）

### 5. 加密解密功能
- AES-CBC 模式加密和解密
- 支持自定义 32 字节密钥
- 自动生成和处理 IV（初始化向量）
- Base64 编码的加密结果

## 技术特点

- **目标框架**：.NET Standard 2.1（兼容 .NET Core 3.0+ 和 .NET 5.0+）
- **依赖少**：主要依赖 System.Text.Json
- **易用性**：提供流畅的 API 设计
- **安全性**：包含安全警告和最佳实践提示

## 安装

### NuGet 包安装

```bash
dotnet add package WebExtensions
# 或在 Visual Studio 的 NuGet 包管理器中搜索 "WebExtensions"
```

### 手动引用

1. 下载最新的 NuGet 包
2. 在 Visual Studio 中右键项目 → 管理 NuGet 包 → 浏览 → 上传本地包

## 使用示例

### Cookie 管理

```csharp
using WebExtensions.src;

// 创建 Cookie 管理器
var cookieManager = new CookieManager();

// 设置单个 Cookie
cookieManager.SetCookie("sessionId", "abc123");

// 批量设置 Cookie（通过字典）
var cookiesDict = new Dictionary<string, string>
{
    {"user", "admin"},
    {"token", "xyz789"}
};
cookieManager.SetCookie(cookiesDict);

// 通过字符串批量设置 Cookie
cookieManager.SetCookie("user=admin; token=xyz789; loggedIn=true");

// 从 WebView2 导入 Cookie（异步）
await cookieManager.ImportFromWebView2Async(webViewCookieManager);

// 导出 Cookie 到 WebView2（异步）
await cookieManager.ExportToWebView2Async(webViewCookieManager);

// 获取 Cookie 值
string tokenValue = cookieManager.GetCookieValue("token");

// 检查 Cookie 是否存在
bool hasSessionCookie = cookieManager.HasCookie("sessionId");
```

### HTTP 请求

```csharp
using WebExtensions.src;

// 创建 HTTP 请求对象
var http = new HttpRequestClass();

// 设置请求参数
http.SetUrl("https://api.example.com/data")
    .SetMethod("POST")
    .SetHeaders(new Dictionary<string, string>
    {
        {"Content-Type", "application/json"},
        {"Authorization", "Bearer token123"}
    })
    .SetBody(JsonSerializer.Serialize(new { id = 1, name = "Test" }));

// 发送请求
var response = await http.SendAsync();

// 获取响应内容
var responseContent = response.Content;
var statusCode = response.StatusCode;
```

### JSON 处理

```csharp
using WebExtensions.src;

// 验证 JSON 字符串
string jsonString = "{\"name\": \"John\", \"age\": 30}";
if (EasyJson.IsValidJson(jsonString))
{
    // 解析为动态对象
    dynamic data = EasyJson.ParseJsonToDynamic(jsonString);
    string name = data.name;
    int age = data.age;
}
```

### 实用工具功能

```csharp
using WebExtensions.src;

// URL 参数排序（按 ASCII 码）
string queryString = "b=value2&a=value1&c=value3";
string sortedQuery = Utility.SortUrlParameters(queryString);
// 结果: a=value1&b=value2&c=value3

// 全URL参数排序
string url = "https://example.com/api?b=2&a=1";
string sortedUrl = Utility.SortUrlParametersInFullUrl(url);
// 结果: https://example.com/api?a=1&b=2

// 查询字符串转字典
Dictionary<string, string> parameters = Utility.QueryStringToDictionary("name=John&age=30");

// 字典转查询字符串
string query = Utility.DictionaryToQueryString(parameters);

// 生成随机码
string randomCode = Utility.refreshCode();

// 获取当前时间戳（13位）
string timestamp = Utility.GetTimeStamp();

// 解析URL中的查询参数
Dictionary<string, string> urlParams = Utility.ParseQueryString("https://example.com/api?name=John&age=30");

// 获取URL中的查询字符串部分
string queryOnly = Utility.GetQueryString("https://example.com/api?name=John&age=30");
// 结果: name=John&age=30
```

### 加密解密功能

```csharp
using WebExtensions.src;

// 使用默认密钥加密
string plaintext = "Hello, this is a secret message!";
string encrypted = AesCrypt.Encrypt(plaintext);
Console.WriteLine("加密结果: " + encrypted);

// 使用默认密钥解密
string decrypted = AesCrypt.Decrypt(encrypted);
Console.WriteLine("解密结果: " + decrypted);

// 使用自定义密钥（必须是32字节长度）
string customKey = "01234567890123456789012345678901"; // 32字节密钥
string encryptedWithCustomKey = AesCrypt.Encrypt(plaintext, customKey);
string decryptedWithCustomKey = AesCrypt.Decrypt(encryptedWithCustomKey, customKey);

// 注意事项：密钥必须为32字节长度，否则会抛出ArgumentException异常
```

## 项目结构

```
WebExtensions/
├── src/
│   ├── Http.cs           # HTTP 请求和 Cookie 管理实现
│   ├── Json.cs           # JSON 处理实现
│   ├── Utility.cs        # 实用工具类，包含URL处理、时间戳等功能
│   └── AesCrypt.cs       # AES加密解密实现
├── WebExtensions.csproj  # 项目配置文件
└── README.md             # 项目文档
```

## 注意事项

1. **SSL 证书验证**：在生产环境中，建议保持 SSL 证书验证启用（默认设置），仅在测试环境中禁用。

2. **异步操作**：库中包含异步方法，建议在使用时正确处理异步操作。

3. **错误处理**：实现时请添加适当的异常处理机制。

## 贡献

欢迎提交 Issues 和 Pull Requests 来帮助改进这个库。

## 许可证

[MIT License](LICENSE)