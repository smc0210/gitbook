# Vue.js


## 1. Prepare before

1. `Chrome broswer`
2. `Atom` or etc `IDE Tools`
3. `Node.js`
4. Vue.js devtools - Chrome extension

{% hint style="info" %}
확장프로그램 세부설정에서 파일 URL에 대한 액세스 허용을 체크 해줘야 file:// 경로도 개발자도구에서 인식
{% endhint %}

`Atom` 추천 테마, 패키지
- `seti-ui`
- `atom-material-syntax-dark`
- `language-vue`

## 2. Getting Start

```xml
<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <div id="app">
      {{ message }}
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.2/dist/vue.js"></script>
    <script>
      new Vue({
        el: '#app',
        data: {
          message: 'Hello Vue.js!'
        }
      });
    </script>
  </body>
</html>
```
## 3. [Simple ES6](https://github.com/smc0210/toyVuejs/tree/master/ES6)

## 4. [Life Cycle](https://github.com/smc0210/toyVuejs/tree/master/Lifecycle)

## 5. [Component](https://github.com/smc0210/toyVuejs/tree/master/Component)

## 6. [Route](https://github.com/smc0210/toyVuejs/tree/master/Route)

## 7. [Template](https://github.com/smc0210/toyVuejs/tree/master/Template)



