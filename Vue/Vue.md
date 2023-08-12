# Vue
## MVVM 패턴
* MVVM 패턴은 Model, View,  ViewModel을 분리하여 독립적인 개발을 할 수 있도록 하여 테스트, 유지보수, 재사용성을 높인 패턴이다.
* Vue는 MVVM 패턴의 ViewModel 레이어에 해당하는 화면단 프레임워크이다.

### 구성 요소
#### Model
* 데이터와 비즈니스 로직을 다루는 역할을 한다.
* 데이터베이스, 네트워크 요청, 데이터 처리 등을 수행한다.

#### View
* 사용자에게 보이는 UI를 나타내며, 사용자의 입력을 받고 화면에 정보를 표시하는 역할을 한다.
* 비즈니스 로직은 포함하지 않는다.

#### ViewModel
* View와 Model사이의 중개자 역할을 수행한다.
* 뷰가 필요로 하는 데이터와 명령을 제공하고, 뷰에서 발생한 이벤트와 입력을 처리하여 Model과 상호작용한다.
* 또한 데이터 바인딩을 통해 뷰의 상태를 업데이트한다.

### MVVM 패턴의 흐름
#### 1. View 초기화 및 표시
* 애플리케이션 시작 시 View가 초기화되고 화면에 표시된다.
* View는 사용자에게 UI를 보여주는 역할을 한다.

#### 2. 사용자 입력 이벤트 처리
* 사용자가 화면에서 동작을 수행하면 이벤트가 View에 전달된다. (버튼 클릭, 텍스트 입력 등의 이벤트)
* DOM Event Listener를 사용해 이벤트를 감지하고 처리한다.

#### 3. View -> ViewModel
* View에서 발생한 이벤트가 ViewModel로 전달된다.
* ViewModel은 이러한 이벤트를 받아들여 처리하거나 데이터를 갱신한다.

#### 4. ViewModel 비즈니스 로직 수행
* ViewModel은 받은 이벤트를 기반으로 필요한 비즈니스 로직을 수행한다.
* 데이터의 처리, 계산, API 요청 등을 수행하여 View에 필요한 데이터를 준비한다.

#### 5. ViewModel 데이터 갱신 및 데이터 바인딩
* 비즈니스 로직이 완료되면 ViewModel은 View에 보여줄 데이터를 갱신한다.
* 데이터를 갱신하면 데이터 바인딩을 통해 View에 자동으로 반영된다.

#### 7. ViewModel -> View
* View는 ViewModel에서 받은 데이터를 이용하여 화면을 업데이트한다.
* 데이터 바인딩을 통해 이미 업데이트된 데이터가 View에 반영된다.

#### 8. 사용자와 View의 상호작용 반복
* 사용자와 View 간의 상호작용이 계속 반복되고, ViewModel은 데이터 처리와 업데이트를 계속 수행하며 View의 상태를 관리한다.
* DOM Event Listener를 통해 이벤트가 감지되고 위의 과정이 반복된다.

## Plugins
### Vue.js Plugin
* 인텔리제이에서 Vue.js개발을 위한 공식 플러그인이다.
* Vue 파일의 코드 하이라이팅, 자동완성, 템플릿 미리보기, 컴포넌트 등록 등 다양한 기능을 제공한다.

### ESLint Integration
* ESLint와 통합되어 JavaScript 코드에서 문제를 감지하고 코드 스타일을 관리할 수 있도록 도와준다.
* 프로젝트 설정에서 ESLint를 활성화하고 사용자 정의 규칙을 적용할 수 있다.

### Prettier Plugin
* 코드 포맷팅을 도와주는 플러그인으로, 인텔리제이에서 Prettier를 통합하여 코드 스타일을 일관성있게 유지할 수 있다.

### Vuetify.js Plugin
* Vue.js 개발 중에 Vuetify를 사용한다면 Vuetify.js Plugin을 활용하여 Vuetify 구성 요소의 코드 자동 완성과 문서를 쉽게 접근할 수 있다.

### Live Templates
* Live Templates를 활용하면 자주 사용하는 코드 조각을 미리 정의하여 효율적으로 활용할 수 있다.
* Vue 템플릿이나 컴포넌트 작성 시 자주 사용하는 코드를 미리 설정하여 더 빠르게 개발할 수 있다.

## Reactivity
* 뷰의 핵심으로, 데이터의 변화를 라이브러리에서 감지해서 알아서 화면에 그려주는 것이다.

### Object.defineProperty()
* 객체의 속성을 재정의 하는 함수이다.
* 이 함수를 이용하여 동적으로 화면을 그릴 수 있다.

```javascript
let div = document.querySelector('#app');
let viewModel = {};

Object.defineProperty(viewModel, 'str', {
  // str 프로퍼티에 접근할 때마다 접근이라는 메시지를 콘솔에 출력
  get: function() {
    console.log('접근');
  },
  // str 프로퍼티에 값을 할당할 때마다 할당과 함께 새로운 값을 콘솔에 출력하고 해당 값을 div엘리먼트의 내용으로 설정
  set: function(newValue) {
    console.log('할당', newValue);
    div.innerHTML = newValue;
  }
})

```

## Instance
* 인스턴스는 뷰로 개발할 때 필수로 생성해야 하는 코드이다.

```html
<body>
  <div id="app">
      
  </div>
<script>
  let vm = new Vue({
    el: '#app',
    data: {
      message: 'hi'
    }
  });
</script>
</body>
```
* 위 예제는 el에서 정의한 app이라는 id를 가진 태그를 찾아 인스턴스를 붙인다.
* 인스턴스를 붙여야 Vue의 다양한 기능과 속성들을 사용할 수 있다.
* 인스턴스를 생성하면 인스턴스는 Root 컴포넌트가 된다.

## Component
* 화면의 영역을 영역 별로 구분해서 관리하는 것이다.
* 컴포넌트의 핵심은 영역을 구분하여 재사용성을 높이는 것이다.

### 전역 컴포넌트와 지역 컴포넌트
* 전역과 지역으로 컴포넌트를 생성할 수 있다.
```html
<script>
  // 전역 컴포넌트
  Vue.component('app-header', {
    template: '<h1>Header</h1>'
  });
    
  new Vue({
    el: '#app',
    // 지역 컴포넌트
    components: {
      'app-footer' : {
        template: '<footer>footer</footer>'
      }
    }
  });
</script>
```
* 인스턴스가 여러 개 있으면 전역 컴포넌트는 어떤 인스턴스에서도 다 사용 가능하지만, 지역 컴포넌트는 해당 컴포넌트를 선언한 인스턴스에서만 사용 가능하다.

## 컴포넌트 통신
* 데이터는 한 방향으로만 움직이는 것이 아니라 유기적으로 다양한 통신을 하는 경우가 대다수이기 때문에 컴포넌트 통신 규칙이 필요하게 된다.

### props(프롭스)
* 상위 컴포넌트에서 하위로 데이터를 내려줄 때 사용한다.
* 상위 컴포넌트에서 하위 컴포넌트를 적용할 때 값을 설정해서 사용할 수 있다.
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <!-- <app-header v-bind:프롭스 속성 이름="상위 컴포넌트의 데이터 이름"></app-header> -->
    <app-header v-bind:propsdata="message"></app-header>
    <app-content v-bind:propsdata="num"></app-content>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    var appHeader = {
      template: '<h1></h1>',
      props: ['propsdata']
    }
    var appContent = {
      template: '<div></div>',
      props: ['propsdata']
    }

    new Vue({
      el: '#app',
      components: {
        'app-header': appHeader,
        'app-content': appContent
      },
      data: {
        message: 'hi',
        num: 10
      }
    })
  </script>
</body>
</html>
```

### emit(이벤트)
* 하위컴포넌트에서 상위컴포넌트로 이벤트를 올려줄 때 사용한다.
* 하위 컴포넌트의 특정 부분에서 사용되는 메서드를 상위 컴포넌트에서 정의할 수 있다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <p></p>
    <!-- <app-header v-on:하위 컴포넌트에서 발생한 이벤트 이름="상위 컴포넌트의 메서드 이름"></app-header> -->
    <app-header v-on:pass="logText"></app-header>
    <app-content v-on:increase="increaseNumber"></app-content>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    var appHeader = {
      template: '<button v-on:click="passEvent">click me</button>',
      methods: {
        passEvent: function() {
          this.$emit('pass');
        }
      }
    }
    var appContent = {
      template: '<button v-on:click="addNumber">add</button>',
      methods: {
        addNumber: function() {
          this.$emit('increase');
        }
      }
    }

    var vm = new Vue({
      el: '#app',
      components: {
        'app-header': appHeader,
        'app-content': appContent
      },
      methods: {
        logText: function() {
          console.log('hi');
        },
        increaseNumber: function() {
          this.num = this.num + 1;
        }
      },
      data: {
        num: 10
      }
    });
  </script>
</body>
</html>
```

### 같은 레벨에서의 컴포넌트 통신 방법
* 상위에서 하위, 하위에서 상위 간의 통신이 아닌 같은 레벨에서의 컴포넌트 통신이 필요할 수 있다.
* 하위 컴포넌트1과 하위 컴포넌트2가 있을때, 하위 컴포넌트1에서 상위컴포넌트로 emit으로 통신 시 파라미터 값을 넘겨주고 상위 컴포넌트와 하위 컴포넌트2를 props로 통신 시 사용하는 변수값을 emit을 정의한 메서드에서 변경해주는 방법이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <!-- props로 값을 넣어줌 -->
    <app-header v-bind:propsdata="num"></app-header>
    <!-- pass를 deliverNum으로 정의 -->
    <app-content v-on:pass="deliverNum"></app-content>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script>
    var appHeader = {
      template: '<div>header</div>',
      props: ['propsdata']
    }
    var appContent = {
      template: '<div>content<button v-on:click="passNum">pass</button></div>',
      // 상위 컴포넌트로 통신할 때, 파라미터를 넘겨줌
      methods: {
        passNum: function() {
          this.$emit('pass', 10);
        }
      }
    }

    new Vue({
      el: '#app',
      components: {
        'app-header': appHeader,
        'app-content': appContent
      },
      data: {
        num: 0
      },
      methods: {
        // value는 하위 컴포넌트에서 올라온 값
        deliverNum: function(value) {
          this.num = value;
        }
      }
    })
  </script>
</body>
</html>
```

## router
* 뷰 라우터는 뷰 라이브러리를 이용하여 싱글 페이지 애플리케이션을 구현할 때 사용하는 라이브러리이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="app">
    <div>
      <!--a태그와 같은 페이지 이동 태그-->
      <router-link to="/login">Login</router-link>
      <!-- <a href="/login">Login</a> -->
      <router-link to="/main">Main</router-link>
    </div>
    <!-- router 컴포넌트가 뿌려질 영역-->
    <router-view></router-view>
  </div>
    
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
  <script>
    let LoginComponent = {
      template: '<div>login</div>'
    }
     let MainComponent = {
      template: '<div>Main</div>'
    }
  let routergg = new VueRouter({
    // 페이지의 라우팅 정보가 배열로 담김
    routes: [
      {
        // 페이지의 url 
        path: '/login',
        // 해당 url에서 표시될 컴포넌트
        component: LoginComponent
      },
      {
        path: '/main',
        component: MainComponent
      }
    ]
  });

  new Vue({
    el: '#app',
    router: routergg
  });
  </script>
</body>
</html>

```

## axios
* javascript의 ajax와 같은 비동기 통신을 위한 라이브러리이다.
* 뷰에서 공식적으로 권장하는 오픈 소스이다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Axios</title>
</head>
<body>
  <div id="app">
    <button v-on:click="getData">get user</button>
    <div>
      {{ users }}
    </div>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  <script>
    new Vue({
      el: '#app',
      data: {
        users: []
      },
      methods: {
        getData: function() { 
          var vm = this;
          axios.get('https://jsonplaceholder.typicode.com/users/')
            .then(function(response) { //성공
              console.log(response.data);
              vm.users = response.data; // vm 대신 this를 쓰면 axios를 바라보게됨
            })
            .catch(function(error) {
              console.log(error);
            });
        }
      }
    })
  </script>
</body>
</html>

```
