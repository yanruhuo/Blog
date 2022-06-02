# Snippets

## 001 Fisher–Yates Shuffle 洗牌算法

```javascript
const shuffle = (arr) => {
  let i = arr.length
  let j
  while (i) {
    j = Math.floor(Math.random() * i--)
    ;[arr[i], arr[j]] = [arr[j], arr[i]]
  }
  return arr
}
```

## 002 数字千分位格式化

```javascript
const toThousandFilter = (num) => {
  return (+num || 0).toString().replace(/^-?\d+/g, (m) => m.replace(/(?=(?!\b)(\d{3})+$)/g, ','))
}
```

## 003 日期格式化

```javascript
const parseTime = (time, format = '{y}-{m}-{d} {h}:{i}:{s}') => {
  if (arguments.length === 0) return null
  let date
  if (typeof time === 'object') {
    date = time
  } else {
    if (typeof time === 'string' && /^[0-9]+$/.test(time)) {
      time = parseInt(time)
    }
    if (typeof time === 'number' && time.toString().length === 10) {
      time = time * 1000
    }
    date = new Date(time)
  }
  const formatObj = {
    y: date.getFullYear(),
    m: date.getMonth() + 1,
    d: date.getDate(),
    h: date.getHours(),
    i: date.getMinutes(),
    s: date.getSeconds(),
    a: date.getDay(),
  }
  const time_str = format.replace(/{(y|m|d|h|i|s|a)+}/g, (result, key) => {
    let value = formatObj[key]
    // Note: getDay() returns 0 on Sunday
    if (key === 'a') {
      return ['日', '一', '二', '三', '四', '五', '六'][value]
    }
    if (result.length > 0 && value < 10) {
      value = '0' + value
    }
    return value || 0
  })
  return time_str
}
```

## 004 动态加载 JS 文件

```javascript
const loadJS = (url) => {
  return new Promise((resolve) => {
    const recaptchaScript = document.createElement('script')
    recaptchaScript.setAttribute('src', url)
    recaptchaScript.async = true
    recaptchaScript.onload = () => resolve()
    document.head.appendChild(recaptchaScript)
  })
}

const loadAssets = async () => {
  const assets = [
    'https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.0/jquery.min.js',
    'https://cdnjs.cloudflare.com/ajax/libs/jqueryui/1.12.1/jquery-ui.min.js',
  ]

  for (const url of assets) {
    await loadJS(url)
  }
}
```

## 005 动态下载文件

```javascript
const downloadFile = (file) => {
  fetch(file.url).then((res) =>
    res.blob().then((blob) => {
      const a = document.createElement('a')
      const url = window.URL.createObjectURL(blob)
      a.href = url
      a.download = file.name
      a.click()
      window.URL.revokeObjectURL(url)
    })
  )
}
```

## 006 获取文件扩展名

```javascript
const getFileExt = (filename) => {
  return filename.slice(((filename.lastIndexOf('.') - 1) >>> 0) + 2)
}
```

## 007 创建独一无二字符串

```javascript
const createUniqueString = () => {
  const timestamp = +new Date() + ''
  const randomNum = parseInt((1 + Math.random()) * 65536) + ''
  return (+(randomNum + timestamp)).toString(32)
}
```

## 008 事件绑定与解绑

```javascript
/**
 * @description 绑定事件 on(element, event, handler)
 */
const on = (function () {
  if (document.addEventListener) {
    return function (element, event, handler) {
      if (element && event && handler) {
        element.addEventListener(event, handler, false)
      }
    }
  } else {
    return function (element, event, handler) {
      if (element && event && handler) {
        element.attachEvent('on' + event, handler)
      }
    }
  }
})()

/**
 * @description 解绑事件 off(element, event, handler)
 */
const off = (function () {
  if (document.removeEventListener) {
    return function (element, event, handler) {
      if (element && event) {
        element.removeEventListener(event, handler, false)
      }
    }
  } else {
    return function (element, event, handler) {
      if (element && event) {
        element.detachEvent('on' + event, handler)
      }
    }
  }
})()
```

## 009 获取视频时长

```javascript
const getVideoDuration = () => {
  const fileObj = this.$refs.file.files[0]
  const video = document.createElement('video')
  video.preload = 'metadata'
  video.onloadedmetadata = () => {
    window.URL.revokeObjectURL(video.src)
    const duration = video.duration
  }
  video.src = URL.createObjectURL(fileObj)
}
```

## 010 判断是否重复操作

```javascript
const isRepeat = (function () {
  const reData = {}
  return function (name = 'default', time = 300) {
    const now = new Date()
    const re = now - (isNaN(reData[name]) ? 0 : reData[name])
    reData[name] = now
    return re <= time
  }
})()
```

## 011 Chunk array

长数组按指定长度拆分为嵌套子数组：

```javascript
const chunk = (arr, size) => Array.from({ length: Math.ceil(arr.length / size) }, (v, i) => arr.slice(size * i, size * i + size))
chunk([1, 2, 3, 4, 5], 2) // [[1, 2], [3, 4], [5]]
```

## 012 DeepFlatten

多层嵌套数组展开：

```javascript
const deepFlatten = (arr) => [].concat(...arr.map((v) => (Array.isArray(v) ? deepFlatten(v) : v)))
deepFlatten([1, [2, 3], [[4, 5], 6]]) // [1, 2, 3, 4, 5, 6]
```

## 013 获取路由参数

如果项目有使用 vue-router 的话，最简单的方式是取 `this.$route.query`。但是有些项目并没有使用 vue-router，这时可以使用 `URLSearchParams`。

```javascript
//  url = "https://xxxx.com?a=1&b=2"
const searchParams = new URLSearchParams(window.location.search)
searchParams.has('a') === true // true
searchParams.get('a') === '1' // true
searchParams.getAll('a') // ["1"]
searchParams.append('c', '3') // "a=1&b=2&c=3"
searchParams.toString() // "a=1&b=2&c=3"
searchParams.set('a', '0') // "a=0&b=2&c=3"
searchParams.delete('a') // "b=2&c=3"
```

由于 `URLSearchParams` 的兼容性，老旧浏览器兼容可以使用下面代码：

```js
function getQueryString(name) {
  var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i')
  var r = location.search.substr(1).match(reg)
  if (r != null) {
    return decodeURIComponent(r[2])
  }
  return null
}
```

## 014 获取路由参数对象

```javascript
const param2Obj = (url) => {
  const search = url.split('?')[1]
  if (!search) {
    return {}
  }
  return JSON.parse('{"' + decodeURIComponent(search).replace(/"/g, '\\"').replace(/&/g, '","').replace(/=/g, '":"').replace(/\+/g, ' ') + '"}')
}
```

## 015 构建 FormData 表单

```javascript
const formBuilder = (obj) => {
  const formData = new FormData()
  Object.keys(obj).forEach((k) => {
    formData.append(k, obj[k])
  })
  return formData
}
```

## 016 AES 加解密

```javascript
const CryptoJS = require('crypto-js')

const key = CryptoJS.enc.Utf8.parse('1234123412ABCDEF') // 密钥
const iv = CryptoJS.enc.Utf8.parse('ABCDEF1234123412') // 密钥偏移量

/**
 * 消息加密
 * @param {*} msg
 */
export const encodeMsg = (msg) => {
  const msgStr = JSON.stringify(msg)
  const srcs = CryptoJS.enc.Utf8.parse(msgStr)
  const encrypted = CryptoJS.AES.encrypt(srcs, key, {
    iv,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
  })
  const encryptedStr = encrypted.ciphertext.toString()
  return encryptedStr
}

/**
 * 消息解密
 * @param {*} msgStr
 */
export const decodeMsg = (msgStr) => {
  const encryptedHexStr = CryptoJS.enc.Hex.parse(msgStr)
  const srcs = CryptoJS.enc.Base64.stringify(encryptedHexStr)
  const decrypt = CryptoJS.AES.decrypt(srcs, key, {
    iv,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7,
  })
  const decryptedStr = decrypt.toString(CryptoJS.enc.Utf8)
  return decryptedStr.toString()
}
```

## 017 求差集

```javascript
// const difference = [users, unUsed].reduce((a, b) => a.filter(c => !b.includes(c)))

const diff = (arr) => {
  return arr.reduce((a, b) => a.filter((c) => !b.includes(c)))
}
diff([[1, 2, 3], [2, 3], [3]]) // [1]
```

## 018 去除 HTML 标签

### 正则表达式

```js
export const removeHtmlTag = (raw) => raw.replace(/<[\s\S]+?>/g, '')
```

### DOM API

```js
export const removeHtmlTag = (raw) => {
  const box = document.createElement('template')
  box.innerHTML = raw
  return box.content.textContent
}
```

## 019 判断合法日期

```js
export const isDate = (raw) => !isNaN(+new Date(raw))
```

利用 Date 构造函数内部的算法，对于无法解析为日期的数据，`date.toString()` 会返回 `Invalid Date`，`date.getTime()` 对应的返回值则是 `NaN`。而算数运算符会调用对象的 `valueOf()` 方法，`date.valueOf()` 的返回值又与 `date.getTime()` 相同。
