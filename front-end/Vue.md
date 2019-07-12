<div align="center">
    <img src="../asset/vue-fam.png" align="center" width="100%" alt="vue">
</div>
<h1 align="center" >Vue Family Updates</h1>

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
        <br/>
        <img src="../asset/itam-games.jpg" />
        </a>
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

[![원본영상 (유튜브))]](https://youtu.be/7lpemgMhi0k)

이 파트는 2018년 3월 뉴 올리언스에서 개최된 Vue Conference의 토크를 기반으로 만들어졋으며 시기에 따라 최신 버전이 아닐 수도 있습니다.

주 내용은 최신화된 Vue 버전에 따라 좀 더 효율적이고 편하게 개발할 수 있는 방법들에 대한 내용이며 크게 아래의 3 분류로 나뉘어 있습니다.

1. 생산성 향상
2. 극단적 수정(??!!)
3. 무한한 가능성

### 생산성 향상

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

Watcher 기능 향상으로 인해 가능해진 첫 번쨰 최적화는 watcher는 문자열을 입력 값으로 받게 하는 것입니다.
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

하지만 때론 짧고 간단한 템플릿을 위해 긴 자바스크립트 import를 하게 됩니다.

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



## 2. Technology


## 5. Setup

### Frontend

1. Clone our repositry from GitHub

```
$ git clone https://github.com/sojournalists/sojournal.git
```

2. Install and set up [Android Studio](https://developer.android.com/studio)

3. Install dependencies

```
$ yarn install
```

4. Run Android emulator from Android Studio

5. Start Application

```
$ yarn android
```

### Backend

1. Clone our repositry from GitHub

```
$ git clone https://github.com/sojournalists/sojournal.git
```

2. Install dependencies

```
$ yarn install
```

3. Setup the Database

```
#Create database
$ createdb triplog

#Drop database
$ dropdb triplog

#Migration
$ yarn knex --knexfile=./db/knexfile.js migrate:latest

#Rollback
$ yarn knex --knexfile=./db/knexfile.js migrate:rollback

#Seed data
yarn knex --knexfile=./db/knexfile.js seed:run
```

3. Run Server

```
$ yarn node db/server.js
```

4. Run Server (Development)

```
$ yarn nodemon db/server.js
```

5. Now you can test server at localhost:4000/graphql

### Hardware

See Hardware [README](./raspberry-pi/README.md)

## 6. Contributions

To contribute to this app, make sure you create a branch and **ALWAYS** make a pull request. **DO NOT EDIT THE MASTER!**

`git checkout -b <branch_name>`

If you want to push your edited files to your remote file, run the following:

`git push <remote_name> <branch_name>`

---

<div align="center">
<b>LICENSE</b>: CC7 TEAM CYAN
</div>
<br/>
<div align="center">
<b>Linkedin</b>: <br/>

[Brian Lee](https://www.linkedin.com/in/briansunghaklee/)&nbsp;&nbsp;&nbsp; |&nbsp;&nbsp;&nbsp;[Omar Kalouti](https://www.linkedin.com/in/omar-kalouti/)&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;[Chaz Wilson](https://www.linkedin.com/in/chaz-wilson/)&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;[Keisuke Mori](https://www.linkedin.com/in/keisuke-mori/)
