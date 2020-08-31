## 前端项目搭建

### 前端项目搭建

**【前端技术栈】**：**vue + Vant UI + less**

**1. Vue CLI创建项目**

![image-20200826170743250](b-1PhoneHub.assets/image-20200826170743250.png)

**2. Vant UI 安装**

```shell
cnpm i vant -S
```

**3. less安装命令**

```shell
cnpm install less less-loader --save
```

**4. 项目启动测试**

```shell
npm run serve
```



### Vant构建首页

#### 商品标签

每款手机都有自己得属性，品牌，特点等。

**Card卡片标签显示这些属性，在循环中遍历自身的属性内容即可展示**

Card商品卡片

```js
<van-card
  num="2"
  price="2.00"
  desc="描述信息"
  title="商品标题"
  thumb="https://img.yzcdn.cn/vant/ipad.jpeg"
/>
```

![image-20200826173831358](b-1PhoneHub.assets/image-20200826173831358.png)

tag商品属性

```js
<template #tags>
	<van-tag v-for="tag in item.tag" color="#f2826a" style="margin-left: 5px;">{{tag.name}}		</van-tag>
</template>
```

<br>

#### 顶部Tag切换

归结于目前手机同质化太严重，很多手机都外观差别不是很明显。标配全面屏 + 多摄像，所以从手机的大体的颜色来分类，对比不同厂商手机的价格。

```js
<van-tabs v-model="active">
  <van-tab title="标签 1">内容 1</van-tab>
  <van-tab title="标签 2">内容 2</van-tab>
  <van-tab title="标签 3">内容 3</van-tab>
  <van-tab title="标签 4">内容 4</van-tab>
</van-tabs>
export default {
  data() {
    return {
      active: 2,
    };
  },
};
```



![image-20200826174943859](b-1PhoneHub.assets/image-20200826174943859.png)

分类信息存放，遍历`categories`数组

![image-20200826175456030](b-1PhoneHub.assets/image-20200826175456030.png)

遍历获取动态数据

```js
<van-tab v-for="index in categories.length" :title="categories[index-1].name" class="tab">
  <van-card v-for="(item,index) in phones"
            :price="item.price"
            :desc="item.desc"
            :title="item.title"
            :thumb="item.thumb"
  >
    <template #tags>
      <van-tag v-for="tag in item.tag" color="#f2826a" style="margin-left: 5px;">{{tag.name}}</van-tag>
    </template>
    <template #footer>
      <van-button round type="info" size="mini" @click="buy(index)">购买</van-button>
    </template>
  </van-card>
</van-tab>
```

**绑定点击事件**

在当前Title上绑定点击事件

```js
<van-tabs @click="onClick">
```

<br>

#### Button购买按钮

选择按钮

**按钮形状**

通过 `square` 设置方形按钮，通过 `round` 设置圆形按钮。

```html
<van-button square type="primary">方形按钮</van-button>
<van-button round type="info">圆形按钮</van-button>
```

![image-20200826180429952](b-1PhoneHub.assets/image-20200826180429952.png)

<br>

#### 主页面搭建成型

- 先遍历Title，外城循环决定内层数据
- 再遍历当前Title下的Card
- 最后遍历某一Card的属性

![image-20200826180945447](b-1PhoneHub.assets/image-20200826180945447.png)

<br>

<hr>


<br>

### sku商品规格

点击蓝色`Button`后跳转到商品规格页

【官方文档示例】

基础用法

```html
<van-sku
  v-model="show"
  stepper-title="我要买"
  :sku="sku"
  :goods="goods"
  :goods-id="goodsId"
  show-add-cart-btn
  reset-stepper-on-hide
  :initial-sku="initialSku"
  @buy-clicked="onBuyClicked"
>

  <!-- 自定义 sku actions -->
  <template #sku-actions="props">
    <div class="van-sku-actions">
      <van-button square size="large" type="warning" @click="onPointClicked">
        积分兑换
      </van-button>
      <!-- 直接触发 sku 内部事件，通过内部事件执行 onBuyClicked 回调 -->
      <van-button
        square
        size="large"
        type="danger"
        @click="props.skuEventBus.$emit('sku:buy')"
      >
        买买买
      </van-button>
    </div>
  </template>
</van-sku>
```

![image-20200826182248970](b-1PhoneHub.assets/image-20200826182248970.png)

> https://vant-contrib.gitee.io/vant/#/zh-CN/sku

根据官方所给的案例，逐步删减（商品只是手机，相较而言无那么多属性）

![image-20200826183206379](b-1PhoneHub.assets/image-20200826183206379.png)

<br>

### sku到地址页面的跳转传值

在上一个商品属性页面跳转到地址页面时，需要传递哪些数据？

- 选择的商品规格
- 购买数量

点击事件，`item`值传递

```js
onBuyClicked(item) {
  console.log(item)
  this.$store.state.specsId = item.selectedSkuComb.s1
  this.$store.state.quantity = item.selectedNum
  this.$router.push('/addressList')
}
```

**router**下配置路由，跳转到指定页面

![image-20200826190748292](b-1PhoneHub.assets/image-20200826190748292.png)

<br>

----------------

### 地址列表

API

![image-20200826191548052](b-1PhoneHub.assets/image-20200826191548052.png)

```js
export default {
  data() {
    return {
      chosenAddressId: '1',
      list: [
        {
          id: '1',
          name: '张三',
          tel: '13000000000',
          address: '浙江省杭州市西湖区文三路 138 号东方通信大厦 7 楼 501 室',
          isDefault: true,
        },
        {
          id: '2',
          name: '李四',
          tel: '1310000000',
          address: '浙江省杭州市拱墅区莫干山路 50 号',
        },
      ],
      disabledList: [
        {
          id: '3',
          name: '王五',
          tel: '1320000000',
          address: '浙江省杭州市滨江区江南大道 15 号',
        },
      ],
    };
  },
  methods: {
    onAdd() {
      Toast('新增地址');
    },
    onEdit(item, index) {
      Toast('编辑地址:' + index);
    },
  },
};
```

**地址选择**：遍历地址`list`集合即可

**新增地址**：导入地址省市区API

**修改地址**：修改时会自动传递（对象）默认值，修改时类比新增地址

将传来的地址对象转为`json`

```js
name: "AddressEdit",
created(){
    let data = JSON.parse(this.$route.query.item)
    this.addressInfo = data
    let index = data.address.indexOf('区')
    if(index < 0) index = data.address.indexOf('县')
    this.addressInfo.addressDetail = data.address.substring(index+1)
},
```

<br>

<hr>


<br>

### 订单详情

**简化了下单步骤，选择地址1s后默认下单成功，显示订单详情**

下单逻辑

```js
axios.post('http://localhost:8181/order/create',orderForm).then(function (resp) {
    if(resp.data.code == 0){
        let instance = Toast('下单成功');
        setTimeout(() => {
            instance.close();
            _this.$router.push('/detail?orderId='+resp.data.data.orderId)
        }, 1000)
    }
})
```

**订单detail页面，就是商品规格和地址栏信息的展示**。将二者的`data`的属性配合**Cell单元格组件**展示在页面

**基础用法**

`Cell` 可以单独使用，也可以与 `CellGroup` 搭配使用，`CellGroup` 可以为 `Cell` 提供上下外边框。

```html
<van-cell-group>
  <van-cell title="单元格" value="内容" />
  <van-cell title="单元格" value="内容" label="描述信息" />
</van-cell-group>
```

![image-20200826193227258](b-1PhoneHub.assets/image-20200826193227258.png)

地址信息展示

```js
<van-cell-group class="goods-cell-group">
  <van-cell>
    <van-col span="16">
      <van-icon name="location-o" style="margin-right: 30px;"/>
      收货人：{{ data.buyerName }}
    </van-col>
    <van-col>{{ data.tel }}</van-col>
    <van-col span="21" style="padding-left: 43px;font-size: 13px">收货地址：{{ data.address }}</van-col>
  </van-cell>
</van-cell-group>
```

Card商品属性展示

```js
<van-card
    :num="data.num"
    :price="data.price"
    :desc="data.specs"
    :title="data.phoneName"
    :thumb="data.icon"
/>
```

<br>

### 订单支付

订单支付为了方便测试和修改，没有接入支付API。默认复合购买条件下，付款成功！

- 订单价格单位为分，单价 *  数量为总金额

![image-20200826193529100](b-1PhoneHub.assets/image-20200826193529100.png)

订单详情展示，数据回显。

--------

【文档参考】

- [Vant](https://vant-contrib.gitee.io/vant/#/zh-CN/cell)
- [Vant 快速上手](https://www.w3cschool.cn/vantlesson/vantlesson-rig235ur.html)
- [Vue.js 开发基础教程](https://www.html.cn/study/4.html)
- [Vue.js 教程](https://www.runoob.com/vue2/vue-tutorial.html)

-------------------



## API接口对接

####  1. 首页数据

**url**

```
GET  /phone/index
```

参数

```
无
```

返回

```json
{
  code: 0,
  msg: "成功"，
  data: {
    categories: [
      {
        name: "极光蓝"，
        type: 1
      }
    ],
    phones: [
      id: 1,
      title: "Honno 8A",
      price: "2800.00",
      desc: "极光蓝",
      tag: [
        {
          name: "720P珍珠屏"
        },
        {
          name: "Micro USB接口"
        }
      ],
      thumb: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg"
    ]
  }
}
```





#### 2. 根据类型查询手机

```
GET /phone/findByCategoryType
```

参数

```
categoryType: 1
```

返回

```json
{
  code: 0,
  msg: "成功"，
  data: [
    {
      id: 1,
      title: "Honno 8A",
      price: "2800.00",
      desc: "极光蓝",
      tag: [
        {
          name: "720P珍珠屏"
        },
        {
          name: "Micro USB接口"
        }
      ],
      thumb: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg"
    }
  ]
}
```



#### 3 . 查询手机规格

```
GET /phone/findSpecsPhoneId
```

参数

```
phoneId: 1
```

返回

```json
{
  code: 0,
  msg: "成功"，
  data: {
    goods: {
      picture: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg"
    },
    sku: {
      tree: [
        {
          k: "规格",
          v: [
            {
              id: 1,
              name: "32GB",
              imgUrl: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg",
              previewImgUrl: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg"
            },
            {
            id: 2,
            name: "64GB",
            imgUrl: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg",
            previewImgUrl: "../static/1a2b8e30-6e98-405f-9a18-9cd31ff96c35.jpg"
            }
          ],
          k_s: "s1"
        }
      ],
      list: [
        {
          s1: 1,
          price: 280000,
          stock_num: 1
        },
        {
          s1: 2,
          price: 320000,
          stock_num: 1
        }
      ],
      price: "2800.00",
      stock_num: 2,
      none_sku: false,
      hide_stock: false
    }
  }
}
```

<img src="../../../Backup/Phuonhub_docs/Document.assets/image-20200801203116956.png" alt="image-20200801203116956" style="zoom:80%;" />

<img src="../../../Backup/Phuonhub_docs/Document.assets/image-20200801203229838.png" alt="image-20200801203229838" style="zoom:80%;" />

#### 4. 查询地址

```
GET /address/list
```

参数

```
无
```

返回

```json
{
  code: 0,
  msg: "成功"，
  data: [
    {
      areaCode: "440303",
      id: 21,
      name: "iqqcode",
      tel: "13678789632",
      address: "陕西省西安市临潼区123号"
    }
  ]
}
```







#### 5. 新增地址

```
POST /address/create
```

参数

```json
{
  name: "iqqcode",
  tel: "13678789632",
  country: "",
  province: "陕西省",
  city: "西安市",
  country: "临潼区",
  areaCode: "710048",
  postalCode: "",
  addressDetail: "123号路",
  isDefault: false
}
```

返回

```json
{
  code: 0,
  msg: "成功",
  data: null
}
```









#### 6. 修改地址

```
PUT /address/update
```

参数

```json
{
  "id": 38,
  "name": "王五",
  "tel": "15849488888",
  "addressDetail": "江汉路189号",
  "areaCode": "01100",
  "province": "北京市",
  "city": "北京市",
  "country": "朝阳区"
}
```

返回

```json
{
  code: 0,
  msg: "成功",
  data: null
}
```



#### 7. 新增订单

```
POST /order/create
```

参数

```json
{
  name: "iqqcode"
  tel: "13678789632"
  address: "陕西省西安市临潼区123号"
  specsId: 1
  quantity: 1
}
```

返回

```json
{
  code: 0,
  msg: "成功",
  data: {
    orderId: "1586242977480760998"
  }
}
```

> `OrderDTO`

#### 8. 订单详情

```
GET /order/detail
```

参数

```
orderId: "1586242977480760998"
```

返回

```json
{
  code: 0,
  msg: "成功",
  data:{
    orderId: "1586242977480760998",
    buyerName: "iqqcode",
    phoneName: "Honor 8A",
    payStatus: 0,
    freight: 10,
    tel: "13636363636",
    address: "陕西省西安市临潼区123号",
    num: 1,
    specs: "32GB",
    price: "2800.00",
    icon: "../static/e84a2e03-7f19-41d2-98a5-a5c16b7e252d.jpg",
    amount: 2800
  }
}
```

> `OrderDetailVO`

#### 9. 支付订单

```
PUT /order/pay
```

参数

```
orderId: "1586242977480760998"
```

返回

```json
{
  code: 0,
  msg: "成功",
  data: {
    orderId: "1586242977480760998"
  }
}
```



## 项目对接

### 【问题思考总结】

1. 前后端对象对接的问题–VO视图对象

2. 前后端如何对接



### 跨域问题

前端启动`Home.vue`

<img src="../../../Backup/Phuonhub_docs/后端文档.assets/image-20200803074901678.png" alt="image-20200803074901678" style="zoom:80%;" />

后端启动`GET  /phone/index`

测试`create`方法，测试resp请求参数

![](b-1PhoneHub.assets/20200803075028-1598839409729.png)

**出现跨域问题：**

```java
Access to XMLHttpRequest at 'http://localhost:8181/phone/index' from origin 'http://localhost:8080' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

![image-20200803074850591](b-1PhoneHub.assets/image-20200803074850591-1598839409729.png)



## 后端项目构建

### VO视图对象

> 根据前端页面的请求将数据封装传递 

![](b-1PhoneHub.assets/20200801164026-1598839409727.png)

<img src="../../../Backup/Phuonhub_docs/后端文档.assets/image-20200801164346017.png" alt="image-20200801164346017" style="zoom:67%;" />

`PhoneCategory`中有5个字段，但是categories仅需要2个，不符合前端请求的规范

严格安装文档，重现封装映射VO

```java
private List<PhoneCategoryVO> categories;

private List<PhoneInfoVO> phones;
```

`@JsonProperty`

**PhoneCategoryVO**转为json对象后，categoryName对应的json对象的名字。理解为二次命名

<img src="../../../Backup/Phuonhub_docs/后端文档.assets/image-20200801184909260.png" alt="image-20200801184909260" style="zoom: 80%;" />

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

![image-20200801182740852](b-1PhoneHub.assets/image-20200801182740852-1598839409728.png)





 