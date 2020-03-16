# vue创建的作业系统  
测试账户：  
学生：V01002，密码V01002
老师：luwei，，密码：luwei  

## 1、引入组件库和插件
1、使用ant-dedign组件库，下载后，在main.js中引入组件库及其样式文件，并通过Vue.use()使用它(Vue是定义的被引入的vue)  
Ant Design同Ant Design Vue不同  
```
import Antd from "ant-design-vue"
import 'ant-design-vue/dist/antd.css'
Vue.use(Antd)
```
2、使用其它的组件库，如Element也是一样的方法引入  
3、引入组件库，可全部引入，也可将其中的一个组件一个个引入，按需引入，不引入全部，例如：  
```
import Notification from "ant-design-vue/lib/notification"
// 在Vue实例中添加$notification这个变量，方便在其它地方使用它
Vue.$notification=Notification
```
4、引入validate库时，会把$validator这个变量注入到vue实例当中来  
5、$validator有一个validateAll方法，它会验证里面的数据，它返回true或false，如果为true，说明表单通过了验证  



## 2、VeeValidate表单验证插件的使用  
1、VeeValidate是用于验证表单的第三方库，它提供多种表单验证方法  
2、VeeValidate的提示默认是英文的，可以引入中文语言包，将其本地化
```
import zh_CN from 'vee-validate/dist/locale/zh_CN';
Validator.localize('zhzh_CN',zh_CN);
```
3、在要验证的表单的input框标签中写入v-validate="'required'"；
v-validatevee-validate的指令，required是验证规则，此required表示当前的input框必须填  
4、表单中可能含有多个input框，所以每个input框写一个name属性  
5、默认会将name的属性值作为提示头，例如name="ab",则提示会写“ab必须填”，所以写一个data-vv-as="用户名"属性，其属性值将作为提示头  
6、errors.has("name属性值")方法，判断youname属性值的这个input框是否有错误  
7、errors这个变量,是用来存错误信息的  
8、errors.first('name')方法，去获取name属性值为“name”这个input框的错误信息  



## 3、ant design组件库的使用  
1、ant design提供了layout，协助进行页面级整体布局。例如简单的上中下布局
```
 <a-layout>
    <a-layout-header></a-layout-header>
    <a-layout-content></a-layout-content>
    <a-layout-footer></a-layout-footer>
  </a-layout>
```
2、prefix	带有前缀图标的 input，例如在input框的前面加上一个icon图标,type为icon的名字  
```
<a-input type="text">
<a-icon slot="prefix" type="user"></a-icon>
        </a-input>
```  
3、suffix	带有后缀图标的 input  
4、按钮,  
type属性用于改变按钮样式，  
htmlType属性，设置 button 原生的 type 值，如下代码可将其设置成submit按钮（在html中显示是submit类按钮，具有submit按钮的行为），submit按钮在点击使会触发form表单的submit事件  
```
<a-button type="primary" htmlType="submit">提交</a-button>
```

## 4、vue页面的显示  
1、这是一个单页面应用，所以不要用到router来切换路由  
2、定义一个数据loggedIn用来判断是否登录，登录还是不登录所展示的页面头部是不一样的，如果没登录就展示登录界面，登录了就展示学生或老师界面    
3、每次打开页面时，先获取到vuex中的user模块中的info和loggedIn数据  
4、没登录之前loggedIn是false  
5、点击登录按钮，验证成功之后会dispatch（调用）vuex的user模块的login方法，异步改变数据  
$store 是挂载在 Vue 实例上的  
store是挂载到vue上，为vue的根实例  
当前vue实例中的$store中有dispatch方法，用来触发actions中的方法  
user有独立的命名空间，所以是触发user下的login方法   
```
 this.$store.dispatch("user/login", this.form);
```
6、此login方法会向登录接口发送一个请求，这个请求需要提交表单中的参数。  
7、请求成功之后，commit  mutations当中的login方法，改变state中的数据，例如使loggeIn变成true，在后台返回的数据当中，有一个is_admin数据，用于判断登录者是学生还是老师  
8、页面的监听属性watch监听到loggedIn发生变化了，触发函数，如果loggedIn为true，就调用loadDetails函数  
9、loadDetails函数会发送请求，根据is_admin的不同，向不同的接口发送请求，得到老师或学生的数据，使用v-if来展示不同页头和组件， 
10、登录后，使用返回的数据渲染不同的页面，  


## 5、vuex状态管理系统使用  
1、vuex的模块化，将相关的数据提出来放到一个js文件当中，在暴露的时候给一个属性namespaced。设为true，这样就使得当前的user模块有独立的作用域(命名空间)。例如：还有另一个模块student，在student模块中也有一个数据loggedIn，设置namespa:true后，两个不同模块中的loggedIn就是不同的数据了
2、在vuex的index.js（中央容器）文件中，写一个modules属性，它是一个对象，存放所有的模块，把user写进去  
3、vuex有如下几部分  
state  
getters  
mutations  
actions  
3、使用辅助函数获取vuex中的数据，代替的是如下代码，因为要经常使用如下冗长的代码而使用了辅助函数  
```
return this.$store.state.user.logedIn
```
获取state下的user中的logedIn数据  
4、使用辅助函数
```
mapState("user", ["info", "loggedIn"]),
```
获取user中的info和loggedIn，所以第一个参数是"user"  


## 6、vuex-persistedstate自动缓存插件  
1、不使用vuex-persistedstate插件的话，就需要将数据存储到localStorage中，每次打开页面的时候取localStorage中去取  
在login函数（登录的时候）
```
存
localStorage.setItem("eStudy",JSON.stringify({
    loggedIn:true,
    info:JSON.stringify(data)
}))
取
const eStudy=JSON.parse(localStorage.getItem("eStudy))||{}
```

2、在vuex的index.js文件中引入vuex-persistedstate，  
```
import createPersistedState from "vuex-persistedstate";
```
3、在vuex中使用插件，在index.js中写一个plugins属性，它是一个数组，存放所有的插件，把createPersistedState写进去  
4、createPersistedState写成一个函数执行方式，它的参数是一个对象，  
1）key: "eStudy", //key为存储持久状态的密钥  
2）paths: ["user"],//path为可以部分保留状态的所有路径的数组。 如果没有给出路径，则完整状态将保留。 必须使用点符号指定路径。  
//如果使用模块，请包含模块名称，下面的user为模块名称  
// 会自动把user中的值存到localStorage中去  
**刷新页面，或者下次打开页面的时候，就去localStorage中去取**  
3）subscriber,插件的钩子函数,初始化的时候执行  
在初始化的时候，调用user中的checkedLogin函数，检查有没有过期  
参数store是当前vuex的实例  
```
subscriber(store) {
      store.dispatch("user/checkedLogin")
      return function (handler) {
        return store.subscribe(handler)
      }
    }
```
5、user/checkedLogin函数用于检验token有没有过期，发送此请求需要在首部中把Token传过去  
common，设置到所有的headers上
```
api.defaults.headers.common["Token"]=context.state.info.token;
```

## 7、axios的使用  
1、axios使vue推荐的第三方库，用于发送ajax请求的  
2、一般将其放在utils文件夹中，在index.js文件引入  
```
import axios from "axios";
```
3、创建axios实例  
baseURL，请求的基础地址  
```
const api=axios.create({
    baseURL:"http://sandbox_api.estudy.chanke.xyz",
})
// 给所有的post请求配置headers，设置Content-Type的值
api.defaults.headers.post["Content-Type"]="application/json"
```
4、拦截器  
1）请求拦截器，在请求之前把请求拦截  
```
api.interceptors.request.use(()=>{},()=>{})
```
2）回复拦截器，在回复之前把回复拦截  
```
api.interceptors.response.use((response)=>{
    //成功的话，只返回data中的data，这样请求的到的数据就会只有这部分（拦截掉了不要的数据）
     return response.data.data;
},(error)=>{
    //error.config存储的是请求的配置
    const config=error.config;

    //失败的话,打开一个提示框
    Vue.$notification.error({
            message:"API ERROR",
            description:error.response.data.errorMessage,
    
        })
})
```
$notification.error()方法，打开一个错误提示框  

5、axios使用：
返回promise对象，第一个参数是请求地址，第二个参数是传的参数，第三个参数是配置  
```
api.post("/auth/refreshToken",{},{_slient:true})
```
## 8、ant design中的notification的使用  
1、引入Notification，在vue中注入了$Notification这个变量  
2、 $notification.error是一个方法，用于打开一个错误提示框  
3、这个方法的参数是一个对象，有两个必填属性  
message通知提醒标题，必选，  
description	通知提醒内容，必选  


## 9、a-table组件的使用  
### 1、a-table组件用于展示行列数据。  
当有大量结构化的数据需要展现时；  
当需要对数据进行排序、搜索、分页、自定义操作等复杂行为时。  

### 2、a-table组件的几个属性  
1）dataSource为数据数组，这个表格要渲染哪个数组的数据  
指定表格的数据源 dataSource 为一个数组。
2）columns为表格每列的配置描述，它的值是一个数组，数组的每项是一个对象，在对象中进行每列的配置  
配置对象的几个属性  
2.1）title，这一列的标题  
2.2）dataIndex为表格的这列要渲染数组中的哪个数据   
2.3）customRender 自定义的数据展示  
需要进行两个数据的拼接，是数据的复杂处理，不用dataIndex了  
customRender函数的参数分别为当前行的值，当前行数据，行索引  
return 返回值就是渲染展示的值  
```
customRender(text, record, index) {
            // text当前行的值
            // record当前行的数据
            //index当前行的索引
            return `${record.start_time} -- ${record.end_time}`;
          }
```

 3）pagination属性 分页器，设为 false 时不展示和进行分页  
 4）rowKey	表格行 key 的取值，可以是字符串或一个函数  
 ```
 例如：函数写成如下这样  
 :rowKey="assignments=>assignments.assignment_id"
 ```
 5)scopedSlots,在表格中要添加dom时，使用插槽， 
 ```
scopedSlots: {
            //在当前列中渲染operation这个插槽
            customRender: "operation"
          }
```
 在插槽中使用slot-scope,接收参数   
 ```
 例如：operation这个插槽中的元素，它们要使用这一行的数据，通过slot-scope来接受  
 text当前行的值  
 record当前行的数据  
 <template slot="operation" slot-scope="text,record">
 ```

### 3、a-upload组件，用于上传  
```
<a-upload
          name="file"
          :multiple="true"
          :action="uploadHost"
          :headers="{Token:info.token}"
          :data="{id:record.assignment_id}"
          @change="changeUpload"
          :showUploadList="false"
        >
          <!-- name 发到后台的文件参数名 -->
          <!-- multiple 是否支持多选文件，ie10+ 支持。开启后按住 ctrl 可选择多个文件。-->
          <!-- action 上传的地址-->
          <!-- headers 设置上传的请求头部，IE10 以上有效-->
          <!-- data	上传所需参数或返回上传参数的方法 -->
          <!-- change	上传文件改变时的状态 上传中、完成、失败都会调用这个函数。 -->
          <!-- showUploadList 是否展示 uploadList(进度条), 可设为一个对象，用于单独设定 showPreviewIcon 和 showRemoveIcon -->
          <a-button>
            <a-icon type="upload" />上传
          </a-button>
        </a-upload>
```
### 4、下载、上传  
1）下载作业，接口要求在首部传token  
使用a标签的href下载，通过？传参  
```
a标签中包裹一个按钮  
例1：  
<a :href="`${downloadHost}All?id=${record.assignment_id}`">
              <a-button icon="download">下载全部</a-button>
            </a>

例2：  
<a :href="`${downloadHost}?id=${record.id}&type=student`">
            <a-button icon="download">下载</a-button>
          </a>
```   

2）上传  
a-upload标签中包裹一个按钮  
action上传地址  
headers传首部  
data传数据  
```
<a-upload
              :action="uploadHost"
              :headers="{Token:info.token}"
              :data="{id:revisingWork.id}"
            >
              <a-button icon="upload">上传</a-button>
            </a-upload>
```

### 5、a-modal组件，模态对话框。  
1、需要用户处理事务，又不希望跳转页面以致打断工作流程时，可以使用 Modal 在当前页面正中打开一个浮层，承载相应的操作。
另外当需要一个简洁的确认框询问用户时，可以使用 Modal.confirm() 等语法糖方法。  
2、visible属性值默认为false不显示，当属性值为true就显示，所以通过修改visible的属性值来控制modal框的显示与否  
3、cancel事件	点击遮罩层或右上角叉或取消按钮的回调  

### 6、点击“展开”按钮  
点击“展开”按钮后，只展示当前行，并展示当前点击行的table框（使用v-if），其余行被隐藏  
“展开”按钮换成“收起”按钮（v-if和v-else）   
其余行被隐藏，使用筛选，筛选要写的几个属性  
1）key如果使用可控的筛选和排序，那么务必指定 column.key，所以在配置对象中写如下属性  
```
key: "operation"
```
2）filteredValue 筛选的受控属性，外界可用此控制列的筛选状态，值为已筛选的 value 数组  
当使用可控的筛选和排序的时候，就要使用2）filteredValue   
什么时可控的筛选和排序？  
例如点击另一个按钮的时候，而不是点击表格的表头，此时也能产生筛选动作  
**文档中的table组件下的筛选与排序、可控的筛选和排序**  
```
filteredValue: this.revisingAssignment
            ? [this.revisingAssignment.assignment_id]
            : null,
```


3）onFilter 本地模式下，确定筛选的运行函数, 使用 template 或 jsx 时作为filter事件使用  
筛选的回调函数  
filteredValue有值的时候会进行调用，进行筛选  
filteredValue值为null的时候就不进行筛选  
所以点击展开的时候让filteredValue有值，点击收起的时候就让filteredValue变成null  
并且把filteredValue传入到onFilter函数中的value形参  
record，每一行的数据，它会一行一行的去进行比较  
```
对每一行进行筛选， 
哪一行的record.assignment_id和传入的value相同，就只留下这一行  
value就是上面filteredValue的值  
onFilter(value, record) {
            return value == record.assignment_id;
          }
```
4）filters  使用列的 filters 属性来指定需要筛选菜单的列，是一个数组  
有filters属性，会在表头有筛选符号  
```
类似于input框，value是值，text是展示的文本  
filters: [
        {
          text: 'Joe',
          value: 'Joe',
        },
        {
          text: 'Jim',
          value: 'Jim',
        },
        {
          text: 'Submenu',
          value: 'Submenu',
          children: [
            {
              text: 'Green',
              value: 'Green',
            },
            {
              text: 'Black',
              value: 'Black',
            },
          ],
```
5）sorter 表头会有三角形的排序按钮    
sorter: function(rowA, rowB) { ... }， rowA、rowB 为比较的两个行数据。  
6）sortDirections: ['ascend' | 'descend']改变每列可用的排序方式，切换排序时按数组内容依次切换  
ascend上升 descend下降  
**表格使用了筛选，因为需要实时监控columns这个数据，所以columns需要写到计算属性当中，而不能写到data当中了**  

 ### 7、日期选择框  
 format 属性，可以自定义日期显示格式   
 例如：dateFormat: "YYYY-MM-DD",    

 change事件是 日期范围发生变化的回调   
 第一个参数date为一个长度为2的数组，它是开始日期和截止日期的两个对象，包含日期的更多信息(包含_d,_isAMomentObject,_isUTC,_locale等属性)   
 第二个参数dateString为一个长度为2的数组，它是开始日期和截止日期的两个字符串   
 ```
 <a-range-picker :format="dateFormat" @change="onChange" />
 ```
  ### 8、表单  
```
<a-form>
<a-form-item></a-form-item>
<a-form-item></a-form-item>
<a-form-item></a-form-item>
</a-form>
```
用a-form-item标签包裹，这个标签有一个label属性，这个标签类似于label标签   
