### WebAPI2中使用Swagger并支持Owin OAuth2.0

1. 安装NugetPackage (配置Owin见其他文档)

`Swashbuckle`


2. 添加对“/token”的支持，否则不会显示在Swagger UI中

```c#
 public class AuthorizeDocumentFilter : IDocumentFilter
    {
        /// <summary>
        /// 
        /// </summary>
        /// <param name="swaggerDoc"></param>
        /// <param name="schemaRegistry"></param>
        /// <param name="apiExplorer"></param>
        public void Apply(SwaggerDocument swaggerDoc, SchemaRegistry schemaRegistry, IApiExplorer apiExplorer)
        {
            swaggerDoc.paths.Add("/token", new PathItem
            {
                post = new Operation
                {
                    tags = new List<string> { "Token" },
                    consumes = new List<string>
                {
                    "application/x-www-form-urlencoded"
                },
                    parameters = new List<Parameter> {
                    new Parameter
                    {
                        type = "string",
                        name = "grant_type",
                        required = true,
                        @in = "formData"
                    },
                    new Parameter
                    {
                        type = "string",
                        name = "username",
                        required = true,
                        @in = "formData"
                    },
                    new Parameter
                    {
                        type = "string",
                        name = "password",
                        required = true,
                        @in = "formData"
                    }
                }
                }
            });
        }
    }
```

3. 过滤请求，判断请求是否添加**[Authorize]**标识，若需要授权，则添加**Authorization**参数

```c#
public class HttpAuthHeaderFilter : IOperationFilter
    {
        /// <summary>
        /// 应用
        /// </summary>
        /// <param name="operation"></param>
        /// <param name="schemaRegistry"></param>
        /// <param name="apiDescription"></param>
        public void Apply(Operation operation, SchemaRegistry schemaRegistry, ApiDescription apiDescription)
        {
            if (operation.parameters == null)
                operation.parameters = new List<Parameter>();
            var filterPipeline = apiDescription.ActionDescriptor.GetFilterPipeline(); //判断是否添加权限过滤器
            var isAuthorized = filterPipeline.Select(filterInfo => filterInfo.Instance).Any(filter => filter is IAuthorizationFilter); //判断是否允许匿名方法 
            var allowAnonymous = apiDescription.ActionDescriptor.GetCustomAttributes<AllowAnonymousAttribute>().Any();
            if (isAuthorized && !allowAnonymous)
            {
                operation.parameters.Add(new Parameter { name = "Authorization", @in = "header", description = "授权认证", required = true, type = "string" });
            }
        }
    }
```
4. 配置SwaggerConfig.cs，添加

```c#
public class SwaggerConfig
    {
        public static void Register()
        {
            var thisAssembly = typeof(SwaggerConfig).Assembly;

            GlobalConfiguration.Configuration
                .EnableSwagger(c =>
                {
                    //添加对/token的支持
                    c.DocumentFilter<AuthorizeDocumentFilter>();
                    //添加Authorization参数
                    c.OperationFilter<HttpAuthHeaderFilter>();
                    c.SingleApiVersion("v1", "WebapiIdentity");                  
 				   c.IncludeXmlComments(GetXmlCommentsPath());
                    })
                .EnableSwaggerUi(c =>
                    {
                    });
        }
        private static string GetXmlCommentsPath()
        {
        //右键——项目——属性——生成——设置xml目录和名称，保持一致
            return System.String.Format(AppDomain.CurrentDomain.BaseDirectory + @"\bin\Swagger.xml");
        }
    }
```
