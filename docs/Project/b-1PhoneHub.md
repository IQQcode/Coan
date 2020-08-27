## 【问题思考总结】

1. 前后端对象对接的问题–VO视图对象

2. 前后端如何对接





## 1. VO视图对象

> 根据前端页面的请求将数据封装传递 

![](b-1PhoneHub.assets/20200801164026.png)

<img src="后端文档.assets/image-20200801164346017.png" alt="image-20200801164346017" style="zoom:67%;" />

`PhoneCategory`中有5个字段，但是categories仅需要2个，不符合前端请求的规范

严格安装文档，重现封装映射VO

```java
private List<PhoneCategoryVO> categories;

private List<PhoneInfoVO> phones;
```

`@JsonProperty`

**PhoneCategoryVO**转为json对象后，categoryName对应的json对象的名字。理解为二次命名

<img src="后端文档.assets/image-20200801184909260.png" alt="image-20200801184909260" style="zoom: 80%;" />

> 在Service层还是展示的java对象原有名字，在Controller返回为json时`JsonProperty`才做替换

```java
public class PhoneCategoryVO {
    @JsonProperty("name")
    private String categoryName;
    @JsonProperty("type")
    private Integer categoryType;
}
```



**PhoneService数据测试**

- dataVO

![image-20200801182740852](b-1PhoneHub.assets/image-20200801182740852.png)





## 2. 前后端对接

### 2.1 跨语问题

前端启动`Home.vue`

后端启动`GET  /phone/index`

测试`create`方法，测试resp请求参数

![](b-1PhoneHub.assets/20200803075028.png)

**出现跨域问题：**

```java
Access to XMLHttpRequest at 'http://localhost:8181/phone/index' from origin 'http://localhost:8080' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

![image-20200803074850591](b-1PhoneHub.assets/image-20200803074850591.png) 