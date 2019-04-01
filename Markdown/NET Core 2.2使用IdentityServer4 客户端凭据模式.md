## .NET Core 2.2  IdentityServer4 Client Credentials 客户端凭据模式

## 一、授权服务


1. 创建`IdentityServerCenter` Web API项目

```
dotnet new webapi --name IdentityServerCenter
```

2. 添加`IdentityServer4` NuGet包

```
## VS包管理器
Install-Package IdentityServer4 -Version 2.3.2

## .NET CLI
dotnet add package IdentityServer4 --version 2.3.2

```

3. 添加`Config.cs`配置文件

```c#
using System.Collections.Generic;
using IdentityServer4.Models;

namespace IdentityServerCenter
{
    public class Config
    {
        //获取ApiResource列表
        public static IEnumerable<ApiResource> GetApiResources()
        {
            return new List<ApiResource> {
                new ApiResource("api","My Api")
            };
        }
		//获取Client列表
        public static IEnumerable<Client> GetClients()
        {
            return new List<Client> {
               new Client(){
                   ClientId="client",
                   AllowedGrantTypes =GrantTypes.ClientCredentials,
                    ClientSecrets =
                   {
                    new Secret("secret".Sha256())
                   },
                   AllowedScopes={ "api"}
            } };
        }
    }
}

```

4. 编辑`startup.cs`

```c#
public void ConfigureServices(IServiceCollection services)
{
  services.AddIdentityServer()
   .AddDeveloperSigningCredential()
   .AddInMemoryApiResources(Config.GetApiResources())
   .AddInMemoryClients(Config.GetClients());
}        
        
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
   app.UseIdentityServer();
}
```

5. 编辑`launchSettings.json`文件，将IIS和Kestrel的`applicationUrl`都设置为`http://localhost:5000`，将`launchUrl`都设置为`.well-known/openid-configuration`

```json
{
  "$schema": "http://json.schemastore.org/launchsettings.json",
  "iisSettings": {
    "windowsAuthentication": false, 
    "anonymousAuthentication": true, 
    "iisExpress": {
      "applicationUrl": "http://localhost:5000",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "launchUrl": ".well-known/openid-configuration",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "IdentityServerCenter": {
      "commandName": "Project",
      "launchBrowser": true,
      "launchUrl": ".well-known/openid-configuration",
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

6. `dotnet run`运行IdentityServerCenter，浏览器中访问http://localhost:5000/.well-known/openid-configuration，正常情况下可见如下Json字符:

```json
{
    "issuer":"http://localhost:5000",
    "jwks_uri":"http://localhost:5000/.well-known/openid-configuration/jwks",
    "authorization_endpoint":"http://localhost:5000/connect/authorize",
    "token_endpoint":"http://localhost:5000/connect/token",
    "userinfo_endpoint":"http://localhost:5000/connect/userinfo",
    "end_session_endpoint":"http://localhost:5000/connect/endsession",
    "check_session_iframe":"http://localhost:5000/connect/checksession",
    "revocation_endpoint":"http://localhost:5000/connect/revocation",
    "introspection_endpoint":"http://localhost:5000/connect/introspect",
    "device_authorization_endpoint":"http://localhost:5000/connect/deviceauthorization",
    "frontchannel_logout_supported":true,
    "frontchannel_logout_session_supported":true,
    "backchannel_logout_supported":true,
    "backchannel_logout_session_supported":true,
    "scopes_supported":[
        "api",
        "offline_access"
    ],
    "claims_supported":[

    ],
    "grant_types_supported":[
        "authorization_code",
        "client_credentials",
        "refresh_token",
        "implicit",
        "urn:ietf:params:oauth:grant-type:device_code"
    ],
    "response_types_supported":[
        "code",
        "token",
        "id_token",
        "id_token token",
        "code id_token",
        "code token",
        "code id_token token"
    ],
    "response_modes_supported":[
        "form_post",
        "query",
        "fragment"
    ],
    "token_endpoint_auth_methods_supported":[
        "client_secret_basic",
        "client_secret_post"
    ],
    "subject_types_supported":[
        "public"
    ],
    "id_token_signing_alg_values_supported":[
        "RS256"
    ],
    "code_challenge_methods_supported":[
        "plain",
        "S256"
    ]
}
```

7. postman发起Post请求，URL为`http://localhost:5000/connect/token`，参数及返回的Token如下：

| 参数名        | 参数类型  | 参数位置           | 参数值说明                                              |
| ------------- | --------- | ------------------ | ------------------------------------------------------- |
| client_id     | form-data | client             | GetClients方法中声明的你需要调用的客户端的ClientId      |
| client_secret | form-data | secret             | GetClients方法中声明的你需要调用的客户端的Secret        |
| grant_type    | form-data | client_credentials | GetClients方法中声明的你需要调用的客户端的AllowedScopes |

```json
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjQ0MmNiODUzZTc5MWVmYzFmNjFiYzQ5YWVjMmUxOTA2IiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NTE3NTQyODUsImV4cCI6MTU1MTc1Nzg4NSwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDAwIiwiYXVkIjpbImh0dHA6Ly9sb2NhbGhvc3Q6NTAwMC9yZXNvdXJjZXMiLCJhcGkiXSwiY2xpZW50X2lkIjoiY2xpZW50Iiwic2NvcGUiOlsiYXBpIl19.r1ZP6IQ9qhUN7QoUP0zCN_v6kHRB5A7yZoEf8oxapHq1OHN2wvfBUUSAc6gwrQwO8xn0pwtbvrQ8bB3kMEu4g7iUyr80khgEqCtBKMBsTbRiUsMA678e-jm6iFT6Bm0Y-E4WMgz5S26DSu5jZj7VSLAgi5I6B3uyhO28EHUHfOr-88JLuSDhsYr6_m3f9QntAD0MwhX2LFW_XOef5yftzkedmBuGzeNyL9bRbgrkkPIrOD9cEMmlKTxfL0lG1F9g0Vf7B-CGhbwQd2iJ_gxZfxxYrOB4WMuE17h8Bvyv13XbyYsAeuAt_Mm2g29b6yYsaYPRbY9WdAjBhpTtYcSy8w",
    "expires_in": 3600,
    "token_type": "Bearer"
}
```

## 二、Api资源

1. 创建`Api`客户端 Web API项目

```
dotnet new webapi --name Api
```

2. 修改`startup.cs`中注册和使用`Authentication`

```
public void ConfigureServices(IServiceCollection services)
        {
            services.AddAuthentication("Bearer")
             .AddJwtBearer("Bearer", options =>
             {
                 options.Authority = "http://localhost:5000";
                 options.RequireHttpsMetadata = false;
                 options.Audience = "api";
             });
            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        }
        
 public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {   
        	app.UseHttpsRedirection();
            app.UseAuthentication();//此处调用
            app.UseMvc();
        }
```

4. 在ValuesController添加[Authorize]标签

```
 [Route("api/[controller]")]
    [Authorize]
    [ApiController]
    public class ValuesController : ControllerBase
    {
        // GET api/values
        [HttpGet]
        public ActionResult<IEnumerable<string>> Get()
        {
            return new string[] { "value1", "value2" };
        }        
    }
```

4. 编辑`launchSettings.json`文件，将IIS和Kestrel的`applicationUrl`都设置为`http://localhost:5001`，
5. `dotnet run`运行项目，浏览器直接访问http://localhost:5001/api/values，会报错显示401未授权，我们需要根据上文中的方法先用Postman从http://localhost:5000/connect/token获取Token，再访问http://localhost:5001/api/values，在Header添加Authorization，值为Bearer ey........(注意，Bearer和Token之间有个空格，一定不能少)，具体值如下：

```
Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6IjQ0MmNiODUzZTc5MWVmYzFmNjFiYzQ5YWVjMmUxOTA2IiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NTE3NTYwOTMsImV4cCI6MTU1MTc1OTY5MywiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDAwIiwiYXVkIjpbImh0dHA6Ly9sb2NhbGhvc3Q6NTAwMC9yZXNvdXJjZXMiLCJhcGkiXSwiY2xpZW50X2lkIjoiY2xpZW50Iiwic2NvcGUiOlsiYXBpIl19.CxSm8cQsyHdzYWqQEY9RqZPy2XOGWqX3D8zZOp1b9snAWqESaZYo0Ct4v-54xBhq9ARTmwFQ8kenz3bJbWy1_X_RMd2sojg0pACZzHKJYce33B9MlLandWG7om6ly-NQUJ7QaiTrEsz6DGz7e28FcDYesiUU0bUPt_goUFas_M3jA2HrUNTTxEOInrp_WEevtk58FkawvVvoA1xB-Ny8mrcwOhmYaUDHh06cfOwOcPP2PAsP53xcAkeoCAQfR8nHXeKvzG248T-5sS5HRcNYMV0t6UvxAP1I6f80mnKhW6mxXeQ6OvWxhM3xe5w5xB_klqG0d3ReIFCnM87o5JA4vQ
```
## 三、第三方客户端

1. 创建客户端应用client

```
dotnet new console --name Client
```

2. 添加`IdentityModel` NuGet包
```
## VS包管理器
Install-Package IdentityModel  -Version 3.10.6

## .NET CLI
dotnet add package IdentityModel  --version 3.10.6
```

3. 修改项目，默认使用c#7.x版本运行，使Main方法支持async，自行百度
4. 请求从http://localhost:5000拿到获取access_token的端点http://localhost:5000/connect/token，拿到access_token后，请求http://localhost:5001/api/values，代码如下：

```c#
static async Task Main(string[] args)
        {
            // 从元数据中发现端点
            var client = new HttpClient();
            var disco = await client.GetDiscoveryDocumentAsync("http://localhost:5000");
            if (disco.IsError)
            {
                Console.WriteLine(disco.Error);
                return;
            }
            //请求Token
            var tokenResponse = await client.RequestClientCredentialsTokenAsync(new ClientCredentialsTokenRequest
            {
                Address = disco.TokenEndpoint,
                ClientId = "client",
                ClientSecret = "secret",
                Scope = "api"
            });

            if (tokenResponse.IsError)
            {
                Console.WriteLine(tokenResponse.Error);
                return;
            }
            Console.WriteLine(tokenResponse.Json);
            // 请求API
            client = new HttpClient();
            client.SetBearerToken(tokenResponse.AccessToken);

            var response = await client.GetAsync("http://localhost:5001/api/values");
            if (!response.IsSuccessStatusCode)
            {
                Console.WriteLine(response.StatusCode);
            }
            else
            {
                var content = await response.Content.ReadAsStringAsync();
                Console.WriteLine(JArray.Parse(content));
            }
            Console.ReadKey();
        }
```



