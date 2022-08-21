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

