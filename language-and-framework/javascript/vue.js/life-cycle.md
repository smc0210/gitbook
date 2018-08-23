# Life Cycle


### 1. `beforeCreate`
`data`속성과 `methods`속성이 아직 인스턴스에 정의되어 있지 않고, 돔과 같은 화면 요소에도 접근 불가

### 2. `created`
`data`속성과 `methods`속성이 인스턴스에 정의되어 있기 때문에 `this.data` or `this.fetchData()`와 같이 `data`속성과 `methods`속성에 정의된 값에 접근 가능하지만 인스턴스가 화면에 부착되기 전이기 때문에 `template`속성에 정의된 돔 요소에는 접근 불가

### 3. `beforeMount`
`template`속성에 지정한 마크업 속성으로 `render()` 함수로 변환한 후 `el`속성에 지정한 화면 요소(`DOM`)에 **인스턴스를 부착하기 전**에 호출되는 단계

### 4. `mounted`
`el`속성에 지정한 화면요소(DOM)에 인스턴스가 부착된 후 호출되는 단계,
화면 요소를 제어하는 로직을 수행하기 좋은 단계

### 5. `beforeUpdate`
관찰하고 있는 데이터가 변경되면 가상 돔으로 화면을 다시 그리기 전에 호출되는 단계,
변경 예정 데이터 값과 관련된 로직을 미리 넣을 수 있다. (이 단계에서 값을 변경하는 로직을 넣어도 화면이 다시 그려지지는 않는다)

### 6. `updated`
데이터가 변경되고 가상 돔으로 다시 화면을 그리고 나면 실행되는 단계.
데이터 변경 후 화면 요소 제어와 관련된 로직을 추가하지 좋은 단계

{% hint style="info" %}
데이터 값을 갱신하는 로직은 가급적 `beforeUpdate`에 추가하고 `updated`에서는 변경 데이터의 화면요소(DOM)와 관련된 로직을 추가하는 것이 좋다
{% endhint %}

### 7. `beforeDestroy`
뷰 인스턴스가 파괴되기 직전 호출되는 단계.
아직 인스턴스에 접근할 수 있으므로 데이터를 삭제하기 좋은 단계

### 8. `destroyed`
뷰 인스턴스가 파괴되고 나서 호출되는 단계로 뷰 인스턴스에 정의한 모든 속성이 제거되고 하위에 선언한 인스턴스들 또한 모두 파괴


> Life Cycle example

```markup
<!DOCTYPE html>
<html lang="ko" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>Vue Instance Lifecycle</title>
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
                },
                beforeCreate: function(){
                    console.log("beforeCreate");
                },
                created: function(){
                    console.log("created");
                },
                mounted: function(){
                    console.log("mounted");
                    // 여기서 값이 변경되지 않는다면 하단의 update 는 실행되지 않음
                    this.message = 'Hello Vue!';
                },
                updated: function(){
                    console.log("updated");
                }
            });
        </script>
    </body>
</html>
```



