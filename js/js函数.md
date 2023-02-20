## 1. base64转文件

`Base64`图片转`File`

```js
base64toFile(dataurl, filename = "file") {
    let arr = dataurl.split(",");
    let mime = arr[0].match(/:(.*?);/)[1];
    let suffix = mime.split("/")[1];
    let bstr = atob(arr[1]);
    let n = bstr.length;
    let u8arr = new Uint8Array(n);
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new File([u8arr], `${filename}.${suffix}`, {
        type: mime
    });
}
```

`Url`图片转`Base64`

```js
Vue.prototype.getBase64 = function getBase64(img, callback) {
  const reader = new FileReader()
  reader.addEventListener('load', () => callback(reader.result))
  reader.readAsDataURL(img)
}
```

`Url`转成`Base64`

```js
function urltoBase64(url, callback) {
  var Img = new Image(),
  dataURL = '';
  Img.src = url + '?v=' + Math.random();
  Img.setAttribute('crossOrigin', 'Anonymous');
  Img.onload = function() {
    var canvas = document.createElement('canvas'),
    width = Img.width,
    height = Img.height;
    canvas.width = width;
    canvas.height = height;
    canvas.getContext('2d').drawImage(Img, 0, 0, width, height);
    dataURL = canvas.toDataURL('image/jpeg');
    return callback ? callback(dataURL) : null;
  };
}
```

文件图片转Base64

```js
upload(file) {
    var content = file.content;
    var fmg = content.replace(/^data:image\/\w+;base64,/, "");
}
```



## 2. 时间校验和对象转换

```js
exports.install = function (Vue, options) {
  Vue.prototype.dateFormat = function dateFormat(date, fmt) {
    if (!fmt) {
      fmt = 'yyyy-MM-dd HH:mm:ss'
    }
    let ret
    const opt = {
      'y+': date.getFullYear().toString(), // 年
      'M+': (date.getMonth() + 1).toString(), // 月
      'd+': date.getDate().toString(), // 日
      'H+': date.getHours().toString(), // 时
      'm+': date.getMinutes().toString(), // 分
      's+': date.getSeconds().toString() // 秒
      // 有其他格式化字符需求可以继续添加，必须转化成字符串
    }
    for (const k in opt) {
      ret = new RegExp('(' + k + ')').exec(fmt)
      if (ret) {
        fmt = fmt.replace(
          ret[1],
          ret[1].length === 1 ? opt[k] : opt[k].padStart(ret[1].length, '0')
        )
      }
    }
    return fmt
  }
    // 对象转换
  Vue.prototype.requestArrayToJson = function requestArrayToJson(values) {
    var result = ''
    const keyArray = Object.keys(values)
    keyArray.map(key => {
      if (values[key] instanceof Array) {
        values[key].map(e => {
          const childJson = Object.keys(e)
          childJson.map(childKey => {
            result += childKey + '=' + e[childKey] + '&'
          })
        })
      } else {
        result += key + '=' + values[key] + '&'
      }
    })
    return result
  }
}
```

