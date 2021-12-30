# 一、setup细节
## setup执行的时机

1. 在beforeCreate之前执行(一次), 此时组件对象还没有创建
2. this是undefined, 不能通过this来访问data/computed/methods / props
3. 其实所有的composition API相关回调函数中也都不可以

## setup的返回值

1. 一般都返回一个对象: 为模板提供数据, 也就是模板中可以直接使用此对象中的所有属性/方法
2. 返回对象中的属性会与data函数返回对象的属性合并成为组件对象的属性
3. 返回对象中的方法会与methods中的方法合并成功组件对象的方法
4. 如果有重名, setup优先

注意:
1. 一般不要混合使用: methods中可以访问setup提供的属性和方法, 但在setup方法中不能访问data和methods
2. setup不能是一个async函数: 因为返回值不再是return的对象, 而是promise, 模板看不到return对象中的属性数据

## setup的参数

1. setup(props, context) / setup(props, {attrs, slots, emit})
2. props: 包含props配置声明且传入了的所有属性的对象
3. attrs: 包含没有在props配置中声明的属性的对象, 相当于 this.$attrs
4. slots: 包含所有传入的插槽内容的对象, 相当于 this.$slots
5. emit: 用来分发自定义事件的函数, 相当于 this.$emit

# 二、reactive与ref-细节

1. 是Vue3的 composition API中2个最重要的响应式API
2. ref用来处理基本类型数据, reactive用来处理对象(递归深度响应式)
3. 如果用ref对象/数组, 内部会自动将对象/数组转换为reactive的代理对象
4. ref内部: 通过给value属性添加getter/setter来实现对数据的劫持
5. reactive内部: 通过使用Proxy来实现对对象内部所有数据的劫持, 并通过Reflect操作对象内部数据
6. ref的数据操作: 在js中要.value, 在模板中不需要(内部解析模板时会自动添加.value)

# 三、 计算属性与监视

## computed函数:

- 与computed配置功能一致
- 只有getter
- 有getter和setter

## watch函数

- 与watch配置功能一致
- 监视指定的一个或多个响应式数据, 一旦数据变化, 就自动执行监视回调
- 默认初始时不执行回调, 但可以通过配置immediate为true, 来指定初始时立即执行-第一次
- 通过配置deep为true, 来指定深度监视

## watchEffect函数

不用直接指定要监视的数据, 回调函数中使用的哪些响应式数据就监视哪些响应式数据
默认初始时就会执行第一次, 从而可以收集需要监视的数据
监视数据发生变化时回调

# 四、生命周期

## vue2 -> vue3
 
beforeCreate -> 使用 setup()

created -> 使用 setup()

beforeMount -> onBeforeMount

mounted -> onMounted

beforeUpdate -> onBeforeUpdate

updated -> onUpdated

beforeDestroy -> onBeforeUnmount

destroyed -> onUnmounted

errorCaptured -> onErrorCaptured

## 新增的钩子函数

组合式 API 还提供了以下调试钩子函数：

- onRenderTracked
- onRenderTriggered

# 五、toRefs

把一个响应式对象转换成普通对象，该普通对象的每个 property 都是一个 ref

应用: 当从合成函数返回响应式对象时，toRefs 非常有用，这样消费组件就可以在不丢失响应式的情况下对返回的对象进行分解使用

问题: reactive 对象取出的所有属性值都是非响应式的

解决: 利用 toRefs 可以将一个响应式 reactive 对象的所有原始属性转换为响应式的 ref 属性

# 六、ref获取元素

利用ref函数获取组件中的标签元素

功能需求: 让输入框自动获取焦点