# 一、请求拦截

```javascript
//设置Vue原型属性
Vue.prototype.appkey = 'U2FsdGVkX19WSQ59Cg+Fj9jNZPxRC5y0xB1iV06BeNA=';

这是axios
//axios拦截器，在发起请求之前执行
axios.interceptors.request.use(config => {

//处理post请求参数, 进行参数序列化
if (config.method == 'post') {

//序列化post请求参数
let paramsString = '';
for (let key in config.data) {
  paramsString += `${key}=${config.data[key]}&`;
}


//重新赋值config.data
config.data = paramsString.slice(0, -1);


// 

}

//必须返回config
return config;
})
```



# 二、 插件安装

vscode 插件vuter

VSCode 快速生成vue模板插件——Vue VSCode Snippets

Live Server

Chinese (Simplified) 

暂时不用
open in browser

Open In Default Browser

# 三、传参方式


通过这方方式传参   name就是你路由配置的时候这个页面定的名字     params: 后面的话就是你要 传递的参数，   这种方式传递参数的缺点就是 页面刷新就没 了
`this.$router.push({name: 'Detail', params: {pid}});`

还有这种跳转方式  这种跳转后页面传递的参数  页面刷新还是会保存的

```javascript
query:?{ // url的query字符串网址?后面的参数解析出来的对象
    wd:?"iPhone",
    aa:?"test"
    } 
}
```



返回上一级

`this.$router.go(-1);`

# 四、安装axios

配置axios

```javascript
npm i axios vue-axios --save

在main.js引入
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)

```

里面的post请求还需要配置一下main.js里面的东西

# 五、安装node

去官网下载

测试 `node -v`

然后测试 `npm -v`

如果显示报错
是内部不是命令行就环境问题需要配置

打开电脑属性-高级系统设置

环境变量 

找到两个path（找到安装路径复制丢上去）

大招如果怎么搞都不行的话

那么我们就卸载重装
直到他自动配上环境

# 六、安装vant

安装vant

`npm i vant@latest-v2 -S`

安装按需导入vant组件插件
  `cnpm babel-plugin-import --save-dev`

报错就用这个
`cnpm i babel-plugin-import -D`

在`babel.config.js`文件添加以下代码

```js
  module.exports = {
    plugins: [
      ['import', {
        libraryName: 'vant',
        libraryDirectory: 'es',
        style: true
      }, 'vant']
    ]
  }
```

在main.js写入以下代码

```javascript
//  导入vant框架的组件 
import { Button } from 'vant'

  //全局注册
  Vue.use(Button)

//测试Button组件
  <van-button type="info">信息按钮</van-button>
```





配置rem, px自动转换rem

安装 postcss-pxtorem、lib-flexible插件
  `npm i postcss-pxtorem lib-flexible --save-dev`

 `cnpm i postcss-pxtorem lib-flexible --save-dev`

在项目根目录创建postcss.config.js文件，写入一下内容

```js
module.exports = {
    plugins: {
      autoprefixer: {
        browsers: ['Android >= 4.0', 'iOS >= 8'],
      },
      'postcss-pxtorem': {
        rootValue: 37.5,
        propList: ['*']
      },
    }
  }
```

在main.js导入lib-flexible.js
  `import 'lib-flexible/flexible'`

##### 页面布局时，需要在iphone6标准屏幕进行布局，这样才可以适配所有屏幕

版本问题
`npm i postcss-pxtorem@5.1.1`

# 七、安装vue

在系统变量中安装Vue脚手架工具vue-cli
`npm install -g @vue/cli`

卸载
`npm uninstall -g @vue/cli`

第一步先
`npm install -g @vue/cli`
如果不行装淘宝镜像
`npm install -g cnpm --registry=https://registry.npmmirror.com`

然后再
`cnpm install -g @vue/cli`

然后通过检测版本
`vue -V`

查看是否安装成功

创建过程

`vue create 项目名字`

第三个变蓝色 自定义

流程跟着我来
`Choose Vue version`

`Babel`

`Router`

`Vuex`

`CSS Pre-processors`

口诀
除了选中上面
后面除了选择less以外  可以一路回车

# 八、复活项目

运行会生成依赖
`npm i` 

在运行`npm run serve`  出现

```ruby
PostCSS plugin postcss-pxtorem requires PostCSS 8.
```

版本问题
再装
`npm i postcss-pxtorem@5.1.1`

# 九、正则

然后`npm run serve`就ok了

​    //构造表单验证信息  注册正则

```javascript
   let o = {
      phone: {
        value: this.userRegisterInfo.phone,
        errorMsg: '手机号格式不正确',
        reg: /^1[3-9]\d{9}$/
      },
      password: {
        value: this.userRegisterInfo.password,
        errorMsg: '密码由数字字母下划线组合(6-16字符)',
        reg: /^[A-Za-z]\w{5,15}$/
      },
      nickName: {
        value: this.userRegisterInfo.nickName,
        errorMsg: '昵称由字母数字下划线汉字组合(1-10字符)',
        reg: /^[\w\u4e00-\u9fa5]{1,10}$/
      },
    };
```

使用

    let isPass = validForm.valid(o);