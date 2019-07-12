<div align="center">
    <img src="../asset/itam-games.jpg" />
    <img src="../asset/vue-fam.png" align="center" width="100%" alt="vue">
</div>
<h1 align="center" >Itam Games Frontend Tech Updates</h1>

## Vue, Vuex, Vue Router 등의 기술 업데이트


<div align="center">
    <div >
        ***********************************************************************
        <br/>
        <a href="https://www.itam.games/en">Our official website</a>
        <br/>
        <br/>
        <a href="https://itam.market/ko">Our Our market page</a>
        <br/>
        ***********************************************************************
    </div>
</div>
<div align="center">

[7 Secret Patterns](#1-7patterns)&nbsp;&nbsp;&nbsp; |&nbsp;&nbsp;&nbsp;
[For Future](#2-future)

</div>

---

## 1. 7 Secret Patterns

![원본영상 (유튜브))]](https://youtu.be/7lpemgMhi0k)

이 파트는 2018년 3월 뉴 올리언스에서 개최된 Vue Conference의 토크를 기반으로 만들어졋으며 시기에 따라 최신 버전이 아닐 수도 있습니다.

주 내용은 최신화된 Vue 버전에 따라 좀 더 효율적이고 편하게 개발할 수 있는 방법들에 대한 내용이며 크게 아래의 3 분류로 나뉘어 있습니다.

1. 생산성 향상
2. 극단적 수정(??!!)
3. 무한한 가능성

<br />
<br />

### 생산성 향상

<br />
<br />

1. 똑똑해진 Watchers

Vue 개발자라면 아래와 같은 Watcher 사용 패턴을 경험해보셧을 것 입니다.

```
created() {
    this.fetchUserList()
}
watch: {
    searchText() {
        this.fetchUserList()
    }
}
```

위의 코드는 컴포넌트가 만들어지면 바로 유저 리스트를 불러오며 특정 데이터가 수정될 떄마다 다시 유저 리스트를 불러오는 디자인 패턴입니다. 위의 예시에서 searchText() 같은 경우에는 유저들의 input 값에 따라 특정 리스트의 필터를 걸어주다고 볼 수 있습니다. (예) 입력 값에 따라 친구 목록 필터).

위의 예시 코드는 아래와 같이 좀 더 최적화 할 수 있습니다.

```
created() {
    this.fetchUserList()
}
watch: {
    searchText: 'fetchUserList'
}
```

Watcher 기능 향상으로 인해 가능해진 첫 번쨰 최적화는 watcher가 문자열을 입력 값으로 받게 하는 것입니다.
한단계 더 최적화하여 아래의 코드로 리팩토링 할 수 있습니다.

```
watch: {
    searchText: {
        handler: 'fetchUserList',
        immediate: true
    }
}
```

위의 예시 코드는 watcher의 immediate 값을 이용한 최적화 입니다.
immediate의 값을 true로 설정하면 created 훅을 사용할 필요가 없습니다.
immediate은 컴포넌트가 준비되면 그 즉시 설정된 핸들러를 가동합니다.

<br />
<br />

2.글로벌 (공용) 컴포넌트 등록

복수의 페이지에서 사용되는 컴포넌트들을 불러올 떄 아래와 같은 코드를 자주 사용하게 됩니다.

```
import BaseButton from './base-button'
import BaseIcon from './base-icon'
import BaseInput from './base-input'

export default {
    components: {
        BaseButton,
        BaseIcon,
        BaseInput
    }
}
```

컴포넌트의 공통된 기능 뿐만 아니라 스타일링 등의 이유로 우리는 공용 컴포넌트를 사용합니다.

하지만 때론 짧고 간단한 템플릿 코드를 위해 긴 자바스크립트 import를 하게 됩니다.

개발자라면 귀찮은 일은 고쳐야하는 일이기 떄문에 아래와 같은 솔루션이 제공됩니다.

```
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

// Require in a base component context
const requireComponent = require.context(
    '.', false, /base-[\w-]+\.vue$/
)

requireComponent.keys().forEach(fileName => {
    //Get component config
    const componentConfig = requireComponent(fileName)

    //Get PascalCase name of component
    const componentName = upperFirst(
        camelCase(fileName.replace(/^\.\//,'').replace(/\.\w+$/, ''))

    //Register component globally
    Vue.component(componentName, componentConfig.default || componentConfig)
    )
})
```

위의 글로벌 컴포넌트 등록은 index.js 혹은 main.js 와 같은 어플리케이션의 엔트리 포인트에 등록해도 되지만 강의자는 component/_global.js 같이 컴포넌트 다이렉토리에 등록하고 이를 엔트리 파일에서 import 하는 것을 추천합니다.

<br />
***맨 마지막 componentConfig.default || componentConfig 신텍스는 웹 팩과 관련된 세팅이며 여기에 해설을 적진 않겟습니다.***
<br />
<br />
이렇게 global registration을 한 컴포넌트는 아래와 같이 import 없이 탬플릿에서 사용할 수 있습니다.


```
<BaseInput 
    v-model="searchText"
    @keydown.enter="search"
    />
<BaseButton @click="search">
    <BaseIcon name="search" />
</BaseButton>
```

import 없이 바로 템플렛에서 컴포넌트를 사용할 수 있기 떄문에 모든 컴포넌트를 등록하고 사용하고 싶을 수 있지만 bundle file의 크기가 과도하게 커지는 것을 막기 위해 공용으로 필요한 컴포넌트만 등록해서 쓰는 것을 추천합니다.


<br />
<br />

3. Module 등록

위의 컴포넌트 등록과 비슷한 맥락으로 module 역시 global 등록을 하여 사용할 수 있습니다. Vuex를 통해 Vue state 관리를 경험 하셧다면 아래의 세팅을 사용해 보셧을 것 입니다.


```
import auth from './modules/auth'
import posts from './modules/posts'
import comments from './modules/comments'

export default new Vuex.Store({
    modules: {
        auth,
        posts,
        comments
        ...
    }
})
```

위와 같은 셋업은 다양한 컴포넌트가 요구될 수록 스토어가 커지고 바빠진다는 단점이 있습니다. 이런 단점을 극복하기 위한 해결책은 아래와 같습니다.

```
import camelCase from 'lodash/camelCase'
import requireModule = require.context('.', false, /\.js$/)
const modules = {}

requireModule.keys().forEach(fileName => {
    //Don't register this file as a Vuex module
    if(fileName === './index.js') return

    const moduleName = camelCase(
        fileName.replace(/(\/\/|\.js)/g, '')

    )
    modules[moduleName] = requireModule(fileName)
})

export default modules
```

위의 코드는 글로벌 컴포넌트 등록과 유사한 형태이며 작동 원리도 비슷합니다.
보통 index.js의 형식으로 modules 다이렉토리에 위치합니다. 코드의 내용을 보면 modules 다이렉토리에 잇는 모든 js 파일을 읽으며 파일명을 캐멀 케이스로 변환하고 modules 객체에 등록하고 이 객체를 Vuex module에 등록합니다.. 파일의 이름이 index.js일 경우에는 아무런 동작 없이 return 합니다. 
<br />
<br />

모든 module 에 일관된 세팅을 더하고 싶다면 아래의 예시와 같이 할 수 있습니다.
아래의 예시에서는 module의 namespacing과 관련된 세팅을 추가한 상태입니다.


```
import camelCase from 'lodash/camelCase'
import requireModule = require.context('.', false, /\.js$/)
const modules = {}

requireModule.keys().forEach(fileName => {
    //Don't register this file as a Vuex module
    if(fileName === './index.js') return

    const moduleName = camelCase(
        fileName.replace(/(\/\/|\.js)/g, '')

    )
    modules[moduleName] = {
        namespaced: true,
        ...requireModule(fileName),
    }
})

export default modules
```

위와 같이 세팅하여 팀원들과의 일관된 스탠더드 프랙티스를 통일할 수 있습니다. 그리고 위와 같이 module을 등록하면 

```
import auth from './modules/auth'
import posts from './modules/posts'
import comments from './modules/comments'

export default new Vuex.Store({
    modules: {
        auth,
        posts,
        comments
        ...
    }
})
```

위의 Vuex.Store 세팅을 아래의 코드로 최적화 시킬 수 있습니다.


```
import modules from './modules'

export default new Vuex.Store({
    modules
})
```

<br />
<br />

### 극단적 수정사항

이 항목은 Vue와 Vue library에 조금의 수정된 사항들을 통해 극단적인 효과를 볼 수 있는 수정사항들에 대한 정보입니다.

<br />
<br />

1.더 꺠끗해진 Views

***이 수정 사항은 Vue router 라이브러리에 관련된 수정사항입니다.***

<br />

아래의 코드는 페이지 데이터를 초기 데이터를 부여하고 route 값을 watcher를 통해 관찰하며 resetData를 통해 리셋해줍니다.


```
data() {
    return {
        loading: false,
        error, null,
        post: null
    }
},
watch: {
    '$route': {
        handler: 'resetData',
        immediate: true
    }
},
methods: {
    resetData() {
        this.loading = false
        this.error = null
        this.post = null
        this.getPost(this.$route.params.id)
    },
    getPost(postId) {
        //........
    }
}
```

위의 예시와 같이 굳이 초기 데이터를 리셋 시켜주는 이유는 만약 다음 $route가 같은 컴포넌트를 사용하는 경우, Vue는 최소한의 렌더링을 위해 부분적으로 데이터 초기화를 하기 때문에 엣지 케이스 수정을 위해 데이터 리셋을 시켜주는 것 입니다.
(ex. /post/1 ===> /post/2 와 같이 컴포넌트를 공유하는 라우트)


하지만 초기화 한번을 통해 Vue의 엣지 케이스를 커버할 수 있다면 좀 더 이상적일 것입니다. 아래의 예시는 router-view의 :key 값을 통해 이 문제에 솔루션을 제공합니다.

```
data() {
    return {
        loading: false,
        error, null,
        post: null
    }
},
created () {
    this.getPost(this.$route.params.id)
},
methods: {
    getPost(postId) {
        //........
    }
}


<router-view :key="$route.fullPath"></router-view>
```

위의 코드는 만약 fullPath가 바뀐다면 컴포넌트가 같더라도 초기 상태로 렌더하라는 패턴입니다. 이 패턴을 적용할 경우 라우트 간의 로딩 속도가 좀 더 걸리게 되는 것은 사실이지만 디버깅과 좀 더 예측 가능한 라우팅의 측면에서 고려해 볼 만한 사항입니다.


<br />
<br />

2. 투명 wrapper

***컴포넌트 사이 attribute의 공유에 대한 기능 수정 사항에 대한 파트입니다***


아래는 기본적인 BaseInput 태그의 사용 예시입니다.


```
<template>
    <input
        :value="value"
        @input="$emit('input', $emit.target.value)"
        >
</template>

    <BaseInput @focus.native="doSomething">
```

위의 예시는 focus가 BaseInput 컴포넌트에 있을 때 native를 통해 BaseInput 컴포넌트의 root element의 event를 주시하게 됩니다. 


하지만 아래와 같이 label로 둘러싸게 리팩토링 된다면 BaseInput은 원래 의도대로 작동할 수 없게 됩니다.


```
<template>
    <label>
    {{ label }}
        <input
            :value="value"
            @input="$emit('input', $emit.target.value)"
        >    
    </label>
</template>

    <BaseInput @focus.native="doSomething">
```


위와 같이 추가적인 리팩토링이나 패턴 수정으로 인해 생길 수 있는 문제는 아래의 솔루션을 통해서 사전에 방지할 수 있습니다. 

```
<template>
    <label>
    {{ label }}
        <input
            :value="value"
            v-on="listeners"
        >    
    </label>
</template>



computed: {
    listeners() {
        return {
            ...this.$listeners,
            input: event =>
                this.$emit('input', event.target.value)
        }
    }
}

<BaseInput @focus="doSomething" />
```


위의 예시와 같이 computed에 listeners() 를 통해 부모 자식 컴포넌트 간의 모든 $listener 값을 관리하고 BaseInput의 root element에 v-on을 객체 신텍스로 설정함으로 $listener의 모든 이벤트를 주시할 수 있습니다. 

이렇게 BaseInput을 구성하면서 미래에 리팩토링 및 구조 수정에도 문제없는 컴포넌트 형성을 할 수 있습니다.

하지만 이 방식도 아래의 코드와 같이 BaseInput에 placeholder가 추가된다면 에러를 피할 수 없습니다.


```
<template>
    <label>
    {{ label }}
        <input
            :value="value"
            v-on="listeners"
        >    
    </label>
</template>


<BaseInput 
    placeholder="What's your name?"
    @focus="doSomething" />
```


위의 예시에서 에러가 발생하는 이유는 Vue는 prop으로 지정되지 않은 attribute을 root element로 넘기기 떄문입니다. 만약 Vue의 디폴트 세팅대로 미지정 attribute을 무조건 root element로 넘겨받지 않고 지정한 attribute만을 선택적으로 관리하고 싶다면 아래와 같은 솔루션을 이용할 수 있습니다.


```
<template>
    <label>
    {{ label }}
        <input
            v-bind="$attrs"
            :value="value"
            v-on="listeners"
        >    
    </label>
</template>


<BaseInput 
    placeholder="What's your name?"
    @focus="doSomething" />


inheritAttrs: false
```

inheritAttrs: false는 Vue component config에서 지정할 수 잇는 세팅입니다. 이 옵션은 컴포넌트가 자동으로 attribute을 넘겨받지 않고 v-bind를 통해 지정한 것들만을 넘겨받게 합니다. $attrs는 prop으로 지정되지 않은 모든 attribute들이 저장되어 잇는 객체입니다.


<br />
<br />

### 무한한 가능성

<br />
<br />

***이 파트는 Vue에서 잘 사용되지 않는 가능성들에 대한 설명입니다***

Vue에서 렌더할 떄 단 하나의 root component만 허용된다는 것을 모두 아실 겁니다. 하지만 너무나 자주 겪는 에러이고 또 때로는 이 기본적인 규칙을 지키기 힘들 떄가 있습니다.


```
<template>
    <ul>
        <NavBarRoutes :routes="persistentNavRoutes" />
        <NavBarRoutes
            v-if="loggedIn"
            :routes="loggenInNavRoutes"
        />
        <NavBarRoutes 
            v-else
            :routes="loggedOutNavRoutes
        />
    </ul>
</template>

```

위의 예시는 로그인 상태에 따라 다른 네비게이션 바를 렌더하는 디자인 패턴입니다. 상용화되어 있는 패턴이고 ul을 root component로 하고 있어 이 자체로는 문제가 되지 않지만 NavBarRoutes로 인해 에러가 발생할 수 있습니다.


```
<template>
    <li
        v-for="route in routes"
        :key="route.name"
    >
        <router-link :to="route">
        {{ route.title }}
        </router-link>
    </li>
</template>

```

위와 같이 li의 배열을 출력하려고 할 경우 에러가 발생하게 됩니다.
이는 아래와 같은 함수형 렌더방식을 채택하면서 고칠 수 있습니다. 


```
functional: true,
render(h, { props }) {
    return props.routes.map(route => 
        <li key={route.name}>
            <router-link to={route}>
                {route.title}
            </router-link>
        </li>
    )
}

```


위의 예시와 같이 함수형 렌더 선언을 한다면 복수의 루트 컴포넌트를 렌더할 수 있습니다. 하지만 만약 다른 팀원들과 협업을 하고 계시다면 팀원 모두 함수형 렌더 선언 방식에 대한 지식이 필요할 것 입니다.



2.non-HTML element 렌더링

***이 파트는 webGL 같은 non-HTML 렌더링에 관한 내용입니다.***

<br />
<br />
<br />
<br />
<br />
<br />
<br />






<div align="center">
<b>LICENSE</b>: Internet
</div>
<br/>
<div align="center">
<b>Linkedin</b>: <br/>

|&nbsp;&nbsp;&nbsp;[Brian Lee](https://www.linkedin.com/in/briansunghaklee/)&nbsp;&nbsp;&nbsp;|
