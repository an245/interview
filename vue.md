# Vue 核心概念与面试题

## 1. Vue基础概念

### 核心概念
- **响应式系统**：数据变化自动更新视图
- **组件化**：可复用的UI单元
- **模板语法**：HTML模板 + JavaScript表达式
- **指令**：v-if、v-for、v-model等
- **生命周期**：组件从创建到销毁的过程

### 常问面试题

#### Q1: Vue是什么？有什么特点？
**答案：**
```vue
<!-- Vue是一个渐进式JavaScript框架 -->
<template>
  <div>
    <h1>{{ message }}</h1>
    <button @click="increment">Count: {{ count }}</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello Vue!',
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>

<!-- Vue的特点：
1. 响应式：数据变化自动更新视图
2. 组件化：可复用的UI组件
3. 模板语法：HTML + JavaScript表达式
4. 指令：v-if、v-for、v-model等
5. 渐进式：可以逐步采用Vue功能
-->
```

#### Q2: Vue2和Vue3的主要区别？
**答案：**
```javascript
// Vue2 - Options API
export default {
  data() {
    return {
      count: 0,
      message: 'Hello'
    }
  },
  computed: {
    doubleCount() {
      return this.count * 2
    }
  },
  methods: {
    increment() {
      this.count++
    }
  },
  mounted() {
    console.log('Component mounted')
  }
}

// Vue3 - Composition API
import { ref, computed, onMounted } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const message = ref('Hello')
    
    const doubleCount = computed(() => count.value * 2)
    
    const increment = () => {
      count.value++
    }
    
    onMounted(() => {
      console.log('Component mounted')
    })
    
    return {
      count,
      message,
      doubleCount,
      increment
    }
  }
}

// 主要区别：
// 1. Composition API vs Options API
// 2. 更好的TypeScript支持
// 3. 更好的性能（Proxy响应式）
// 4. 更好的Tree-shaking
// 5. 多个根节点支持
```

## 2. 响应式系统

### 核心概念
- **响应式原理**：Object.defineProperty (Vue2) / Proxy (Vue3)
- **依赖收集**：收集依赖的watcher
- **派发更新**：通知依赖更新
- **计算属性**：基于依赖的缓存计算
- **侦听器**：监听数据变化

### 常问面试题

#### Q3: Vue的响应式原理？
**答案：**
```javascript
// Vue2响应式原理 - Object.defineProperty
function defineReactive(obj, key, val) {
  const dep = new Dep() // 依赖收集器
  
  Object.defineProperty(obj, key, {
    get() {
      // 收集依赖
      if (Dep.target) {
        dep.addSub(Dep.target)
      }
      return val
    },
    set(newVal) {
      if (newVal === val) return
      val = newVal
      // 派发更新
      dep.notify()
    }
  })
}

// Vue3响应式原理 - Proxy
function reactive(target) {
  return new Proxy(target, {
    get(target, key, receiver) {
      const result = Reflect.get(target, key, receiver)
      // 收集依赖
      track(target, key)
      return result
    },
    set(target, key, value, receiver) {
      const result = Reflect.set(target, key, value, receiver)
      // 派发更新
      trigger(target, key)
      return result
    }
  })
}

// 使用示例
const state = reactive({
  count: 0,
  message: 'Hello'
})

// 当访问state.count时，会收集依赖
// 当修改state.count时，会派发更新
```

#### Q4: computed和watch的区别？
**答案：**
```vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double Count: {{ doubleCount }}</p>
    <p>Message: {{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0,
      message: 'Hello'
    }
  },
  computed: {
    // 计算属性 - 基于依赖缓存，只有依赖变化时才重新计算
    doubleCount() {
      console.log('computed: doubleCount')
      return this.count * 2
    }
  },
  watch: {
    // 侦听器 - 监听数据变化，执行副作用
    count(newVal, oldVal) {
      console.log(`count changed from ${oldVal} to ${newVal}`)
    },
    message: {
      handler(newVal, oldVal) {
        console.log(`message changed from ${oldVal} to ${newVal}`)
      },
      immediate: true, // 立即执行
      deep: true // 深度监听
    }
  }
}
</script>

// Vue3 Composition API
<script setup>
import { ref, computed, watch } from 'vue'

const count = ref(0)
const message = ref('Hello')

const doubleCount = computed(() => {
  console.log('computed: doubleCount')
  return count.value * 2
})

watch(count, (newVal, oldVal) => {
  console.log(`count changed from ${oldVal} to ${newVal}`)
})

watch(message, (newVal, oldVal) => {
  console.log(`message changed from ${oldVal} to ${newVal}`)
}, { immediate: true })
</script>
```

## 3. 组件通信

### 核心概念
- **Props**：父组件向子组件传递数据
- **Events**：子组件向父组件传递数据
- **Provide/Inject**：跨层级组件通信
- **EventBus**：事件总线通信
- **Vuex/Pinia**：状态管理

### 常问面试题

#### Q5: Vue组件间通信方式？
**答案：**
```vue
<!-- 1. Props + Events -->
<!-- 父组件 -->
<template>
  <div>
    <ChildComponent 
      :message="parentMessage" 
      @update="handleUpdate"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      parentMessage: 'Hello from parent'
    }
  },
  methods: {
    handleUpdate(newMessage) {
      this.parentMessage = newMessage
    }
  }
}
</script>

<!-- 子组件 -->
<template>
  <div>
    <p>{{ message }}</p>
    <button @click="updateParent">Update Parent</button>
  </div>
</template>

<script>
export default {
  props: {
    message: {
      type: String,
      required: true
    }
  },
  methods: {
    updateParent() {
      this.$emit('update', 'Updated from child')
    }
  }
}
</script>

<!-- 2. Provide/Inject -->
<!-- 祖先组件 -->
<script>
export default {
  provide() {
    return {
      theme: 'dark',
      updateTheme: this.updateTheme
    }
  },
  data() {
    return {
      theme: 'dark'
    }
  },
  methods: {
    updateTheme(newTheme) {
      this.theme = newTheme
    }
  }
}
</script>

<!-- 后代组件 -->
<script>
export default {
  inject: ['theme', 'updateTheme'],
  methods: {
    changeTheme() {
      this.updateTheme('light')
    }
  }
}
</script>

<!-- 3. EventBus -->
<!-- eventBus.js -->
import Vue from 'vue'
export const EventBus = new Vue()

<!-- 组件A -->
<script>
import { EventBus } from './eventBus'

export default {
  methods: {
    sendMessage() {
      EventBus.$emit('message', 'Hello from A')
    }
  }
}
</script>

<!-- 组件B -->
<script>
import { EventBus } from './eventBus'

export default {
  mounted() {
    EventBus.$on('message', (message) => {
      console.log('Received:', message)
    })
  },
  beforeDestroy() {
    EventBus.$off('message')
  }
}
</script>
```

## 4. 生命周期

### 核心概念
- **创建阶段**：beforeCreate、created
- **挂载阶段**：beforeMount、mounted
- **更新阶段**：beforeUpdate、updated
- **销毁阶段**：beforeDestroy、destroyed
- **错误处理**：errorCaptured

### 常问面试题

#### Q6: Vue生命周期详解？
**答案：**
```vue
<template>
  <div>
    <p>{{ message }}</p>
    <button @click="updateMessage">Update</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: 'Hello Vue'
    }
  },
  beforeCreate() {
    console.log('1. beforeCreate - 实例创建前')
    // 此时data和methods还未初始化
    console.log(this.message) // undefined
  },
  created() {
    console.log('2. created - 实例创建后')
    // 此时data和methods已初始化，但DOM还未挂载
    console.log(this.message) // 'Hello Vue'
    // 适合进行API调用、初始化数据
  },
  beforeMount() {
    console.log('3. beforeMount - DOM挂载前')
    // 模板已编译，但还未挂载到DOM
    console.log(this.$el) // undefined
  },
  mounted() {
    console.log('4. mounted - DOM挂载后')
    // DOM已挂载，可以访问DOM元素
    console.log(this.$el) // <div>...</div>
    // 适合进行DOM操作、第三方库初始化
  },
  beforeUpdate() {
    console.log('5. beforeUpdate - 数据更新前')
    // 数据已更新，但DOM还未重新渲染
  },
  updated() {
    console.log('6. updated - 数据更新后')
    // DOM已重新渲染
    // 避免在此阶段修改数据，可能导致无限循环
  },
  beforeDestroy() {
    console.log('7. beforeDestroy - 实例销毁前')
    // 实例还未销毁，可以清理定时器、取消网络请求等
  },
  destroyed() {
    console.log('8. destroyed - 实例销毁后')
    // 实例已销毁，所有子组件也被销毁
  },
  errorCaptured(err, instance, info) {
    console.log('Error captured:', err, instance, info)
    // 捕获子组件错误
    return false // 阻止错误继续传播
  },
  methods: {
    updateMessage() {
      this.message = 'Updated message'
    }
  }
}
</script>

// Vue3 Composition API生命周期
<script setup>
import { 
  onBeforeMount, 
  onMounted, 
  onBeforeUpdate, 
  onUpdated, 
  onBeforeUnmount, 
  onUnmounted 
} from 'vue'

onBeforeMount(() => {
  console.log('beforeMount')
})

onMounted(() => {
  console.log('mounted')
})

onBeforeUpdate(() => {
  console.log('beforeUpdate')
})

onUpdated(() => {
  console.log('updated')
})

onBeforeUnmount(() => {
  console.log('beforeUnmount')
})

onUnmounted(() => {
  console.log('unmounted')
})
</script>
```

## 5. 指令系统

### 核心概念
- **内置指令**：v-if、v-for、v-model、v-show等
- **自定义指令**：用户定义的指令
- **指令钩子**：bind、inserted、update等
- **指令参数**：指令的修饰符和参数

### 常问面试题

#### Q7: 自定义指令的实现？
**答案：**
```javascript
// 全局自定义指令
Vue.directive('focus', {
  // 指令第一次绑定到元素时调用
  bind(el, binding, vnode) {
    console.log('bind')
  },
  // 元素插入到DOM时调用
  inserted(el, binding, vnode) {
    console.log('inserted')
    el.focus()
  },
  // 所在组件的VNode更新时调用
  update(el, binding, vnode, oldVnode) {
    console.log('update')
  },
  // 指令所在组件的VNode及其子VNode全部更新后调用
  componentUpdated(el, binding, vnode, oldVnode) {
    console.log('componentUpdated')
  },
  // 指令与元素解绑时调用
  unbind(el, binding, vnode) {
    console.log('unbind')
  }
})

// 简写形式（bind和update时调用）
Vue.directive('color', (el, binding) => {
  el.style.color = binding.value
})

// 使用指令
<template>
  <div>
    <input v-focus />
    <p v-color="'red'">红色文字</p>
  </div>
</template>

// 局部自定义指令
export default {
  directives: {
    focus: {
      inserted(el) {
        el.focus()
      }
    },
    color: (el, binding) => {
      el.style.color = binding.value
    }
  }
}

// Vue3自定义指令
const app = createApp({})

app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})

// 简写形式
app.directive('color', (el, binding) => {
  el.style.color = binding.value
})
```

## 6. 路由管理

### 核心概念
- **Vue Router**：官方路由管理器
- **路由配置**：路由定义和配置
- **导航守卫**：路由跳转的钩子函数
- **动态路由**：路由参数和查询参数
- **嵌套路由**：多级路由结构

### 常问面试题

#### Q8: Vue Router的使用？
**答案：**
```javascript
// router/index.js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '@/views/Home.vue'
import User from '@/views/User.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/user/:id',
    name: 'User',
    component: User,
    props: true, // 将路由参数作为props传递
    meta: { requiresAuth: true }, // 路由元信息
    beforeEnter: (to, from, next) => {
      // 路由独享守卫
      console.log('Entering user page')
      next()
    }
  },
  {
    path: '/admin',
    component: () => import('@/views/Admin.vue'), // 懒加载
    children: [
      {
        path: 'users',
        component: () => import('@/views/AdminUsers.vue')
      }
    ]
  }
]

const router = new VueRouter({
  mode: 'history', // 使用HTML5 History模式
  routes
})

// 全局前置守卫
router.beforeEach((to, from, next) => {
  console.log('Global beforeEach')
  
  // 检查是否需要认证
  if (to.meta.requiresAuth) {
    const isAuthenticated = checkAuth()
    if (!isAuthenticated) {
      next('/login')
      return
    }
  }
  
  next()
})

// 全局后置钩子
router.afterEach((to, from) => {
  console.log('Global afterEach')
})

export default router

// 组件中使用路由
<template>
  <div>
    <router-link to="/">Home</router-link>
    <router-link :to="{ name: 'User', params: { id: 123 } }">User</router-link>
    <router-view />
  </div>
</template>

<script>
export default {
  methods: {
    goToUser() {
      this.$router.push({ name: 'User', params: { id: 123 } })
    },
    goBack() {
      this.$router.go(-1)
    }
  },
  computed: {
    userId() {
      return this.$route.params.id
    }
  }
}
</script>

// 组件内守卫
export default {
  beforeRouteEnter(to, from, next) {
    // 在渲染该组件的对应路由被confirm前调用
    // 不能获取组件实例this
    next(vm => {
      // 通过vm访问组件实例
    })
  },
  beforeRouteUpdate(to, from, next) {
    // 在当前路由改变，但该组件被复用时调用
    // 可以访问组件实例this
    next()
  },
  beforeRouteLeave(to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例this
    next()
  }
}
```

## 7. 状态管理

### 核心概念
- **Vuex**：Vue2官方状态管理
- **Pinia**：Vue3推荐状态管理
- **State**：应用状态
- **Mutations**：同步修改状态
- **Actions**：异步操作
- **Getters**：状态计算属性

### 常问面试题

#### Q9: Vuex的使用？
**答案：**
```javascript
// store/index.js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0,
    user: null,
    todos: []
  },
  
  mutations: {
    // 同步修改状态
    INCREMENT(state) {
      state.count++
    },
    SET_USER(state, user) {
      state.user = user
    },
    ADD_TODO(state, todo) {
      state.todos.push(todo)
    }
  },
  
  actions: {
    // 异步操作
    async fetchUser({ commit }, userId) {
      try {
        const response = await fetch(`/api/users/${userId}`)
        const user = await response.json()
        commit('SET_USER', user)
      } catch (error) {
        console.error('Failed to fetch user:', error)
      }
    },
    
    increment({ commit }) {
      commit('INCREMENT')
    }
  },
  
  getters: {
    // 状态计算属性
    doubleCount: state => state.count * 2,
    activeTodos: state => state.todos.filter(todo => !todo.completed),
    userFullName: state => {
      if (!state.user) return ''
      return `${state.user.firstName} ${state.user.lastName}`
    }
  },
  
  modules: {
    // 模块化
    auth: {
      namespaced: true,
      state: {
        token: null
      },
      mutations: {
        SET_TOKEN(state, token) {
          state.token = token
        }
      }
    }
  }
})

export default store

// 组件中使用Vuex
<template>
  <div>
    <p>Count: {{ count }}</p>
    <p>Double Count: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
    <button @click="fetchUser(123)">Fetch User</button>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from 'vuex'

export default {
  computed: {
    ...mapState(['count', 'user']),
    ...mapGetters(['doubleCount', 'userFullName'])
  },
  methods: {
    ...mapMutations(['INCREMENT']),
    ...mapActions(['fetchUser']),
    
    increment() {
      this.INCREMENT()
    }
  }
}
</script>

// Vue3 + Pinia
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  
  getters: {
    doubleCount: (state) => state.count * 2
  },
  
  actions: {
    increment() {
      this.count++
    },
    
    async fetchData() {
      const response = await fetch('/api/data')
      const data = await response.json()
      // 更新状态
    }
  }
})

// 组件中使用Pinia
<script setup>
import { useCounterStore } from '@/stores/counter'

const counterStore = useCounterStore()
</script>
```

## 8. 性能优化

### 核心概念
- **懒加载**：组件和路由懒加载
- **虚拟滚动**：长列表优化
- **keep-alive**：组件缓存
- **异步组件**：动态导入组件
- **代码分割**：按需加载

### 常问面试题

#### Q10: Vue性能优化技巧？
**答案：**
```vue
<!-- 1. 组件懒加载 -->
<template>
  <div>
    <button @click="showModal = true">Show Modal</button>
    <Modal v-if="showModal" />
  </div>
</template>

<script>
// 异步组件
const Modal = () => import('./Modal.vue')

export default {
  components: {
    Modal
  },
  data() {
    return {
      showModal: false
    }
  }
}
</script>

<!-- 2. 路由懒加载 -->
const routes = [
  {
    path: '/home',
    component: () => import('@/views/Home.vue')
  }
]

<!-- 3. keep-alive缓存组件 -->
<template>
  <div>
    <keep-alive :include="['UserList', 'ProductList']">
      <router-view />
    </keep-alive>
  </div>
</template>

<!-- 4. 虚拟滚动 -->
<template>
  <div>
    <VirtualList
      :items="items"
      :item-height="50"
      :container-height="400"
    >
      <template #default="{ item }">
        <div>{{ item.name }}</div>
      </template>
    </VirtualList>
  </div>
</template>

<!-- 5. 计算属性缓存 -->
<script>
export default {
  data() {
    return {
      items: [
        { id: 1, name: 'Item 1', price: 100 },
        { id: 2, name: 'Item 2', price: 200 }
      ]
    }
  },
  computed: {
    // 只有依赖变化时才重新计算
    expensiveCalculation() {
      console.log('Computing...')
      return this.items.reduce((sum, item) => sum + item.price, 0)
    }
  }
}
</script>

<!-- 6. 事件委托 -->
<template>
  <div @click="handleClick">
    <button data-action="save">Save</button>
    <button data-action="cancel">Cancel</button>
  </div>
</template>

<script>
export default {
  methods: {
    handleClick(event) {
      const action = event.target.dataset.action
      if (action === 'save') {
        this.save()
      } else if (action === 'cancel') {
        this.cancel()
      }
    }
  }
}
</script>

<!-- 7. 防抖和节流 -->
<script>
import { debounce, throttle } from 'lodash'

export default {
  data() {
    return {
      searchTerm: ''
    }
  },
  methods: {
    // 防抖搜索
    search: debounce(function(term) {
      this.performSearch(term)
    }, 300),
    
    // 节流滚动
    handleScroll: throttle(function(event) {
      this.updateScrollPosition(event.target.scrollTop)
    }, 100)
  }
}
</script>
```

## 9. 测试

### 核心概念
- **单元测试**：测试单个组件
- **集成测试**：测试组件交互
- **Vue Test Utils**：Vue官方测试工具
- **Jest**：测试框架
- **Mock**：模拟依赖

### 常问面试题

#### Q11: Vue组件测试？
**答案：**
```javascript
// Counter.vue
<template>
  <div>
    <p>Count: {{ count }}</p>
    <button @click="increment">Increment</button>
    <button @click="decrement">Decrement</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    },
    decrement() {
      this.count--
    }
  }
}
</script>

// Counter.spec.js
import { mount } from '@vue/test-utils'
import Counter from '@/components/Counter.vue'

describe('Counter', () => {
  test('renders initial count', () => {
    const wrapper = mount(Counter)
    expect(wrapper.text()).toContain('Count: 0')
  })
  
  test('increments count when button is clicked', async () => {
    const wrapper = mount(Counter)
    const button = wrapper.find('button')
    
    await button.trigger('click')
    
    expect(wrapper.text()).toContain('Count: 1')
  })
  
  test('decrements count when decrement button is clicked', async () => {
    const wrapper = mount(Counter)
    const buttons = wrapper.findAll('button')
    const decrementButton = buttons[1]
    
    await decrementButton.trigger('click')
    
    expect(wrapper.text()).toContain('Count: -1')
  })
})

// 异步组件测试
import { mount, flushPromises } from '@vue/test-utils'
import UserProfile from '@/components/UserProfile.vue'

describe('UserProfile', () => {
  test('displays user data after loading', async () => {
    // Mock fetch
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({
          id: 1,
          name: 'John Doe',
          email: 'john@example.com'
        })
      })
    )
    
    const wrapper = mount(UserProfile, {
      props: {
        userId: 1
      }
    })
    
    expect(wrapper.text()).toContain('Loading...')
    
    await flushPromises()
    
    expect(wrapper.text()).toContain('John Doe')
    expect(wrapper.text()).toContain('john@example.com')
  })
})

// 组件交互测试
describe('TodoList', () => {
  test('adds and removes todos', async () => {
    const wrapper = mount(TodoList)
    
    // 添加todo
    const input = wrapper.find('input')
    const addButton = wrapper.find('button')
    
    await input.setValue('Test todo')
    await addButton.trigger('click')
    
    expect(wrapper.text()).toContain('Test todo')
    
    // 删除todo
    const deleteButton = wrapper.find('.delete-button')
    await deleteButton.trigger('click')
    
    expect(wrapper.text()).not.toContain('Test todo')
  })
})
```

## 10. 最佳实践

### 核心概念
- **组件设计**：单一职责原则
- **状态管理**：合理的数据流
- **性能优化**：避免不必要的渲染
- **代码组织**：文件结构规范
- **类型安全**：TypeScript集成

### 常问面试题

#### Q12: Vue开发最佳实践？
**答案：**
```vue
<!-- 1. 组件设计原则 -->
<!-- 单一职责：每个组件只做一件事 -->
<template>
  <div class="user-card">
    <img :src="user.avatar" :alt="user.name" />
    <h3>{{ user.name }}</h3>
    <p>{{ user.email }}</p>
  </div>
</template>

<script>
export default {
  name: 'UserCard', // 组件名称
  props: {
    user: {
      type: Object,
      required: true,
      validator: (value) => {
        return value && typeof value.name === 'string'
      }
    }
  }
}
</script>

<!-- 2. 合理的状态管理 -->
<script>
export default {
  data() {
    return {
      // 相关状态放在一起
      user: null,
      loading: false,
      error: null
    }
  },
  
  computed: {
    // 计算属性用于派生状态
    isLoggedIn() {
      return !!this.user
    },
    
    userDisplayName() {
      return this.user ? this.user.name : 'Guest'
    }
  },
  
  methods: {
    // 方法按功能分组
    async fetchUser() {
      try {
        this.loading = true
        this.error = null
        const user = await this.$api.getUser()
        this.user = user
      } catch (error) {
        this.error = error.message
      } finally {
        this.loading = false
      }
    }
  }
}
</script>

<!-- 3. 性能优化 -->
<template>
  <div>
    <!-- 使用v-show代替v-if（频繁切换） -->
    <div v-show="isVisible">Content</div>
    
    <!-- 使用key优化列表渲染 -->
    <div v-for="item in items" :key="item.id">
      {{ item.name }}
    </div>
    
    <!-- 避免在模板中使用复杂表达式 -->
    <div>{{ formattedDate }}</div>
  </div>
</template>

<script>
export default {
  computed: {
    formattedDate() {
      return this.date.toLocaleDateString()
    }
  }
}
</script>

<!-- 4. 错误处理 -->
<script>
export default {
  data() {
    return {
      error: null
    }
  },
  
  errorCaptured(err, instance, info) {
    console.error('Error captured:', err, info)
    this.error = err.message
    return false
  },
  
  methods: {
    async fetchData() {
      try {
        const data = await this.$api.getData()
        this.data = data
      } catch (error) {
        this.handleError(error)
      }
    },
    
    handleError(error) {
      this.error = error.message
      // 发送错误报告
      this.$errorReporting.captureException(error)
    }
  }
}
</script>

<!-- 5. 代码组织 -->
<!-- components/
  ├── UserCard.vue
  ├── UserList.vue
  └── UserForm.vue
-->

<!-- 6. TypeScript集成 -->
<script lang="ts">
import { defineComponent, PropType } from 'vue'

interface User {
  id: number
  name: string
  email: string
}

export default defineComponent({
  name: 'UserCard',
  props: {
    user: {
      type: Object as PropType<User>,
      required: true
    }
  },
  setup(props) {
    const formattedName = computed(() => {
      return props.user.name.toUpperCase()
    })
    
    return {
      formattedName
    }
  }
})
</script>
```

## 总结

Vue面试重点：
1. **基础概念**：响应式系统、组件化、模板语法、指令
2. **响应式系统**：响应式原理、computed、watch、依赖收集
3. **组件通信**：Props、Events、Provide/Inject、EventBus
4. **生命周期**：创建、挂载、更新、销毁阶段
5. **指令系统**：内置指令、自定义指令、指令钩子
6. **路由管理**：Vue Router、路由配置、导航守卫
7. **状态管理**：Vuex、Pinia、State、Mutations、Actions
8. **性能优化**：懒加载、虚拟滚动、keep-alive、代码分割
9. **测试**：单元测试、集成测试、Vue Test Utils
10. **最佳实践**：组件设计、状态管理、性能优化、代码组织
