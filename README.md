# springmvc-open-api-generator

## 模块介绍

springmvc-open-api-generator 是基于java源码doc生成兼容swagger-ui的OpenAPI 3.0的json工具,对源代码零侵入,上手简单,生成效率高,使用非常方便.(Spring版)

### 1. dawdler-web-api.yml配置文件说明

例:

```yml
version: 1.0
title: "演示服务"
description: "用于演示的文档"
contact:
 name: "jackson.song"
 email: "suxuan696@gmail.com"
 url: "https://github.com/srchen1987"
swagger: "2.0"
host: "localhost"
basePath: "/"
scanPath:
 - "/home/srchen/github/api-demo"
outPath: "/home/srchen/github/api-demo/demo-api.json"
```

说明：

| 名称 | 说明 |
| :-: | :-: |
| version | 版本号 |
| title | 标题 |
| description | 描述 |
| contact | 联系人 相关信息|
| swagger | swagger版本号 |
| host | api地址,例如: 192.168.1.55:8080 |
| basePath | web的basePath |
| scanPath | 扫描路径,Controller或实体对象(数组结构) |
| outPath | 输出json的路径 |

### 2. 使用方法

#### 2.1 基础配置

参考dawdler-web-api.yml文件进行配置,确保scanPath配置正确.

#### 2.2 必要条件

scanPath配置一定要配置正确,确保路径下有Controller(必须使用@Controller注解的才会生效).

方法返回值需要使用@ResponseBody标注,如果非基础类型或String或BigDecimal则需要在scanPath内才会生效.

方法参数值使用@RequestBody标注的对象需要在scanPath内才会生效.

返回的对象需要有@ResponseBody标注,如果不支持的类型则不会解析,比如Map.

#### 2.3 生成api文件

```shell
java -jar springcloud-open-api-generator-0.0.1.RELEASE.jar   /home/srchen/github/api-demo/dawdler-web-api.yml
```

运行后会生成demo-api.json(outPath配置的路径).


#### 2.4 启动swagger-ui

拉取docker镜像 
```shell
docker pull swaggerapi/swagger-ui
```

启动
```shell
docker run -p 80:8080 -e BASE_URL=/swagger -e SWAGGER_JSON=/foo/demo-api.json -v /home/srchen/github/api-demo:/foo swaggerapi/swagger-ui
```

访问 http://localhost/swagger 既可使用.


#### 3. 已支持javaDoc的Tag/注解/对象

##### 3.1 JavaDoc的Tag
1. @Description 用于类或方法的描述信息,支持放在类上或方法上.

2. @param 用于方法参数对应的注释信息,只支持方法上.

注释上有 @param userId 用户ID, 方法参数列表中的userId就会被备注为名字.

例如:
```java
	/**
	 * 
	* @Title: get 
	* @author jackson.song 
	* @date 2022年3月23日
	* @Description 根据用户ID查询用户
	* @param userId 用户ID
	*
	 */
	@RequestMapping(value = "/user/get",method = RequestMethod.GET)
	@ResponseBody
	public User get(String userId){
		return null;
	}

```

##### 3.2 spring mvc 的注解

1. @RestController 标识一个类为Controller,只有此标识才会被扫描生成文档,用于类上.

2. @RequestMapping 标识设置请求api的path,只有此标识才会被扫描生成文档,支持设置类上和方法上.

3. @RequestParam 指定request请求参数名的注解,可以用搭配@param来做注释,注意RequestParam中的value需要与@param的值对应才生效.

4. @PathVariable 获取pathVariable的变量,用于方法参数列表中,可以用搭配@param来做注释.

5. @RequestHeader 获取head中的值,用于方法参数列表中,可以搭配@param来做注释.

6. @RequestBody 标识一个对象通过http body方式传入.

7. @ResponseBody 标识返回对象.
   
##### 3.3 方法参数列表支持的对象类型(dawdler-client-plug中支持的对象)

1. MultipartFile 用于上传文件时使用的对象,可以搭配@param来做注释.

2. java8大基础类型 用于获取http请求参数,可以搭配@param来做注释.

3. String类型 用于获取http请求参数,可以搭配@param来做注释.

4. BigDecimal 用于获取http请求参数,可以搭配@param来做注释.

5. 自定义对象(通过@RequestBody标识时需要http body,如果没有@RequestBody标识,则自定义对象的属性会作为http param参数) 注释只支持在自定义对象中加入注解,注释采用/** 注释 **/ 例如:

```java
public class User {
	/**
	 * 用户ID
	 */
	private Integer userId;
	/**
	 * 用户名
	 */
	private String userName;
	/**
	 * 地址
	 */
	private String address;

	public Integer getUserId() {
		return userId;
	}
	public void setUserId(Integer userId) {
		this.userId = userId;
	}
	public String getUserName() {
		return userName;
	}
	public void setUserName(String userName) {
		this.userName = userName;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
}

```


#### 4. 返回数据类型

1. 8大基础类型及数组

2. String类型及数组

3. BigDecimal及数组

4. List<类型>/Set<类型>/Collection<类型>/Vector<类型>

5. 自定义类,支持泛型.

 