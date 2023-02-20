封装`request`

```js
import axios from "axios";
import { Toast } from "vant";

let loadingCount = 0;
let loading;

function startLoading() {
  if (loadingCount === 0) {
    loading = Toast.loading({
      duration: 0, // 持续展示 toast
      forbidClick: true,
    });
  }
  loadingCount++;
}

function stopLoading() {
  loadingCount--;
  if (loadingCount === 0) {
    loading.clear();
  }
}

export function request(config) {
  let to=15000;
  if(config.timeout){
    to=config.timeout
  }
  const instance = axios.create({
    baseURL: "",
    withCredentials: true,
    timeout: to,
  });
  // request interceptor
  instance.interceptors.request.use(
    (config) => {
      startLoading();
      return config;
    },
    (error) => {
      return Promise.reject(error);
    }
  );

  // response interceptor
  instance.interceptors.response.use(
    (res) => {
      stopLoading();
      const data = res.data;
      if (data.status != "200") {
        console.log("后端返回异常：" + JSON.stringify(data));
        Toast.fail({
          duration: 6000,
          closeOnClick:true,
          message: data.message || "请求出现异常",
        });
        return Promise.reject(data)
      }
      return data;
    },
    (error) => {
      stopLoading();
      console.log("err" + error); // for debug
      Toast.fail(error.message);
      return Promise.reject(error);
    }
  );
  return instance(config);
}
```

`post`请求表单多文件上传

```js
import request from '@/utils/request'

const interface = {
  list: 'mng/sys/**.do',
  add: 'mng/sys/**.do',
  update: 'mng/sys/**.do',
  delete: 'mng/sys/**.do',
}
// 添加 告警配置 信息
// parameter 为 json 对象，parameter.file 为 文件对象
export function uploadFile (parameter) {
  const form = new FormData()
  const keys = Object.keys(parameter)
  // 出现 多个字段时，一次性 赋值给表单
  keys.map(keyNmae => {
    form.set(keyNmae, parameter[keyNmae])
  })
  // 前面赋值的file 为 对象类型，要重写表单 file 字段
  form.set('file', new Blob([JSON.stringify(parameter.file)]))
  return request({
    url: interface.add,
    method: 'post',
    data: form
  })
}
```

升级，使用权限认证

```js
import axios from "axios";
import router from '../router'
import { Toast } from "vant";

let loadingCount = 0;
let loading;

function startLoading() {
  if (loadingCount === 0) {
    loading = Toast.loading({
      duration: 500, // 持续展示 toast
      forbidClick: true,
    });
  }
  loadingCount++;
}

function stopLoading() {
  loadingCount--;
  if (loadingCount === 0) {
    loading.clear();
  }
}

export function request(config) {
  let to=10000;
  if(config.timeout){
    to=config.timeout
  }
  const instance = axios.create({
    baseURL: "/blogApi/",
    // withCredentials: true,
    timeout: to,
  });
  // request interceptor
  instance.interceptors.request.use(
    (config) => {
        /**
         * 30 秒内用户访问触发 自动更新时间戳，保证当前会话有效，超过 5 分钟（后端设计），会话失效，即需要重新登录
         */
        if (localStorage.getItem('token') != undefined && localStorage.getItem('token') != null &&
            localStorage.getItem('userId') != undefined && localStorage.getItem('userId') != null &&
            localStorage.getItem('timestamp') != undefined && localStorage.getItem('timestamp') != null &&
            new Date() - localStorage.getItem('timestamp') > 30000) {
            // 更新 token 时间
            axios({
                method: "post",
                url: "/blogApi/user/updateToken",
                headers:{
                    token: localStorage.getItem('token')
                },
                params: {
                  token: localStorage.getItem('token')
                },
            })
                .then((res) => {
                  if (res.status == 200) {
                    var data = res.data;
                    if (data.status == 200) {
                        localStorage.setItem("token", res.data.data.userToken);
                        localStorage.setItem("userId", res.data.data.id);
                        localStorage.setItem("timestamp", new Date().getTime());
                    } else if (data.status == 502) {
                      Toast.fail({
                        duration: 2000,
                        closeOnClick:true,
                        message: data.msg || "令牌无效！",
                      });
                      localStorage.removeItem("token");
                      return Promise.reject(data.msg);
                    } else if (data.status == 500) {
                      Toast.fail({
                        duration: 2000,
                        closeOnClick:true,
                        message: data.msg || "服务器内部异常",
                      });
                      return Promise.reject(data.msg);
                    } else {
                      Toast.fail({
                        duration: 2000,
                        closeOnClick:true,
                        message: "未知错误",
                      });
                    }
                  }
                }).catch(error => {
                  var errRes = JSON.parse(JSON.stringify(error))
                  var code = errRes.code != undefined ? errRes.code : "";
                  var status = errRes.status != undefined ? errRes.status : "";
                  var message = errRes.message != undefined ? errRes.message : "";
                  if (code != "" || status != "" || message != "") {
                    var uncaught = JSON.parse("{\"code\": \"" + code + "\", \"status\": \"" + status + "\", \"message\": \"" + message + "\"}");
                    return Promise.reject(uncaught);
                  }       
                  return Promise.reject(error);
                })    
            // 如果后台要token这么写：
            // config.headers.token = localStorage.getItem('token')
            // 如果后台要Authorization 这么写：config.headers.Authorization = `Bearer ` + localStorage.getItem('token')
        }
        config.headers.token = localStorage.getItem('token')
      startLoading();
      return config;
    },
    (error) => {
      return Promise.reject(error);
    }
  );

  // response interceptor
  instance.interceptors.response.use(
    (res) => {
      let data = res.data;
      stopLoading();
      if (res.status != "200") {
        console.log("后端返回异常：" + JSON.stringify(data.msg));
        Toast.fail({
          duration: 2000,
          closeOnClick:true,
          message: data.msg || "请求出现异常",
        });
        return Promise.reject('Access denied without obtaining secret key')
      } else {
        if (data && data.status == 502) {
          Toast.fail({
            duration: 2000,
            closeOnClick:true,
            message: data.msg || "令牌异常",
          });
        }
      }
      return data;
    },
    (error) => {
      stopLoading();
      var errRes = JSON.parse(JSON.stringify(error))
      var code = errRes.code != undefined ? errRes.code : "";
      var status = errRes.status != undefined ? errRes.status : "";
      var message = errRes.message != undefined ? errRes.message : "";
      var uncaught = JSON.parse("{\"code\": \"" + code + "\", \"status\": \"" + status + "\", \"message\": \"" + message + "\"}");
      return Promise.reject(uncaught);
    }
  );
  return instance(config);
}
```

