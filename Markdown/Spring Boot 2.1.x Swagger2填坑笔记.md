## Spring Boot 2.1.x Swagger2填坑笔记

### 1.MAVEN依赖

```xml
		<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
```

### 2. 启用Swagger、配置Swagger信息

```java
//启动类上用@EnableSwagger2启用
@EnableSwagger2
@SpringBootApplication
public class FullscreenApplication {

    public static void main(String[] args) {
        SpringApplication.run(FullscreenApplication.class, args);
    }

}
```

```java
//非必须，仅定制Swagger相关信息
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    //指定扫描哪些包
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                //要扫描的包路径（这里无需通过命名空间，太low了）
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))//显示API注解的接口
                .paths(PathSelectors.any())
                .build();
    }

    //配置swagger的基本信息
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Api Docs")
                //创始人
                .contact(new Contact("宇智波鹏", "uchiha-peng.cc", "uchiha-peng@outlook.com"))
                .description("接口文档V0.1")
                //版本
                .version("0.1")
                //描述
                .build();
    }
}
```

### 3. 解决部署后访问Swagger反复弹窗

```java
//允许访问资源，顺便开启跨域支持
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")//设置允许跨域的路径
                .allowedOrigins("*")//设置允许跨域请求的域名
                .allowCredentials(true)//是否允许证书 不再默认开启
                .allowedMethods("GET", "POST", "DELETE", "PUT", "OPTIONS")//设置允许的方法
                .allowedHeaders("*")
                .maxAge(3600);//跨域允许时间
    }


    /*
      添加url地址大小区不敏感配置
     */
    @Override
    public void configurePathMatch(PathMatchConfigurer configurer) {
        AntPathMatcher matcher = new AntPathMatcher();
        matcher.setCaseSensitive(false);
        configurer.setPathMatcher(matcher);
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 解决静态资源无法访问
        registry.addResourceHandler("/**")
                .addResourceLocations("classpath:/static/");
        // 解决swagger无法访问
        registry.addResourceHandler("/swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        // 解决swagger的js文件无法访问
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");

    }
}

```

```java
//Serverlet初始化，必须
public class ServletInitializer extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(FullscreenApplication.class);
    }

}
```

### 4. 控制器和方法的注释（@Api(tags = "注释"）

```java
@RestController
@RequestMapping("/api")//控制器注释，必须这样，否则不显示
@Api(tags = "大屏展示数据相关接口")
public class ApiController {

    @ApiOperation("查询所有硬件数量")//方法注释
    @GetMapping("/getHardware")
    public ResponseEntity<Hardware> getHardware() {
        try {
            Hardware hardware = new Hardware( 2000, 1800, 3500);
            if (hardware != null)
                return ResponseEntity.status(HttpStatus.OK).body(hardware);
            return ResponseEntity.status(HttpStatus.NO_CONTENT).body(null);
        } catch (Exception ex) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(null);
        }
    }
 }
```

### 5. 实体的注释(实体必须驼峰命名，小写开头，否则不显示)

```java
@ApiModel(value = "Hardware",description = "硬件计数")
public class Hardware extends BaseModel implements Serializable {
    @ApiModelProperty("红绿灯")
    private int trafficLight;

    @ApiModelProperty("RSU")
    private int rsu;

    @ApiModelProperty("摄像头")
    private int camera;

    public int getTrafficLight() {
        return trafficLight;
    }

    public void setTrafficLight(int trafficLight) {
        this.trafficLight = trafficLight;
    }

    public int getRsu() {
        return rsu;
    }

    public void setRsu(int rsu) {
        this.rsu = rsu;
    }

    public int getCamera() {
        return camera;
    }

    public void setCamera(int camera) {
        this.camera = camera;
    }

    public Hardware() {
    }

    public Hardware(int trafficLight, int rsu, int camera) {
        this.trafficLight = trafficLight;
        this.rsu = rsu;
        this.camera = camera;
    }
}

```







