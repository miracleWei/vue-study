# vue-study





## vue.js





### 生命周期

https://cn.vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram



#### beforeCreate

在实例被创建之前调用。



#### created

实例已经创建完成。可以访问 `data` 和执行进一步的初始化任务。



#### beforeMount

在挂载开始之前调用，相关的 `render` 函数首次被调用。



#### mounted

实例已经挂载到 DOM 上后调用，此时可以访问到挂载后的 DOM 元素。



#### beforeUpdate

数据更新时，在更新之前调用。





#### beforeUnmount

在卸载之前调用。该钩子函数在 Vue.js 2.x 中的 `beforeDestroy` 功能类似。



#### unmounted

实例已经卸载后调用。此时组件已经从 DOM 中移除。



除了上述常用的生命周期钩子函数，Vue.js 3.x 版本还提供了其他一些新的钩子函数，如 `beforeUpdate`、`onRenderTracked`、`onRenderTriggered` 等，用于更细粒度的追踪和调试组件的渲染行为。

值得注意的是，Vue.js 3.x 引入了 Composition API，其中的 `setup` 函数也可以被认为是一个生命周期钩子函数。`setup` 函数在组件实例被创建之前调用，可以用来进行组件的初始化、数据处理和副作用等操作。

请注意，具体的生命周期钩子函数使用方式和执行时机可以根据实际需求和组件设计进行灵活调整。你可以根据自己的业务逻辑和需求，在适当的钩子函数中编写代码来实现特定的行为和处理逻辑。



![组件生命周期图示](https://cn.vuejs.org/assets/lifecycle.16e4c08e.png)



### 组件 component



#### 父组件向子组件传参 

> props

在 Vue.js 中，你可以通过使用 props 属性来将数据从父组件传递到子组件。以下是一个简单的示例，展示了如何在父组件中向子组件传递参数：

父组件：

``` vue
<template>
  <div>
    <child-component :message="parentMessage"></child-component>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },
  data() {
    return {
      parentMessage: 'Hello from parent component'
    };
  }
};
</script>
```

在上面的示例中，`parentMessage` 是父组件中的数据，它通过 `:message` 将其传递给了子组件。

子组件：

``` vue
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
export default {
  props: {
    message: String
  }
};
</script>
```

在子组件中，你需要使用 `props` 属性来声明接收的参数，这里我们声明了一个名为 `message` 的 prop。

通过上述代码，父组件中的 `parentMessage` 会传递给子组件中的 `message`，然后在子组件的模板中可以直接使用 `message` 来显示父组件传递的值。

注意：在子组件中，props 是只读的，即不能在子组件中修改传递过来的值。如果需要在子组件中修改父组件的数据，可以通过触发事件或使用 Vuex 等状态管理工具来实现。



#### 子组件向父组件传参 

> $emit

在 Vue.js 中，子组件可以通过触发自定义事件的方式向父组件返回数据。以下是一个简单的示例，展示了如何在子组件中向父组件返回数据：

子组件：

``` vue
<template>
  <div>
    <button @click="sendMessage">发送消息给父组件</button>
  </div>
</template>

<script>
export default {
  methods: {
    sendMessage() {
      const message = 'Hello from child component';
      this.$emit('message-sent', message);
    }
  }
};
</script>
```



在上面的示例中，当按钮被点击时，`sendMessage` 方法会被调用。该方法通过 `this.$emit` 方法触发了一个名为 `message-sent` 的自定义事件，并将消息 `message` 作为参数传递给父组件。

父组件：

``` vue
<template>
  <div>
    <child-component @message-sent="handleMessage"></child-component>
    <p>来自子组件的消息：{{ receivedMessage }}</p>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },
  data() {
    return {
      receivedMessage: ''
    };
  },
  methods: {
    handleMessage(message) {
      this.receivedMessage = message;
    }
  }
};
</script>
```

在父组件中，我们在子组件标签上使用 `@message-sent` 监听了子组件触发的 `message-sent` 事件，并将其与 `handleMessage` 方法关联起来。

`handleMessage` 方法接收子组件传递的消息作为参数，并将其赋值给 `receivedMessage` 数据，然后可以在父组件的模板中使用它来显示来自子组件的消息。

通过上述代码，子组件可以通过触发自定义事件的方式将数据传递给父组件，并由父组件处理接收到的数据。



#### 父组件调用子组件的函数

> $refs

要在父组件中调用子组件的函数，你可以使用 Vue.js 的 `ref` 引用和 `$refs` 属性。以下是一个示例，展示了如何在父组件中调用子组件的函数：

子组件：

``` vue
<template>
  <div>
    <button @click="childFunction">子组件函数</button>
  </div>
</template>

<script>
export default {
  methods: {
    childFunction() {
      console.log('子组件函数被调用');
    }
  }
};
</script>
```

在上面的示例中，子组件有一个名为 `childFunction` 的方法，它会在按钮被点击时输出一条日志。

父组件：

``` vue
<template>
  <div>
    <child-component ref="childRef"></child-component>
    <button @click="callChildFunction">调用子组件函数</button>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },
  methods: {
    callChildFunction() {
      this.$refs.childRef.childFunction();
    }
  }
};
</script>
```

在父组件中，我们使用 `ref` 属性给子组件添加了一个引用标识符 `childRef`。

然后，通过 `this.$refs.childRef`，我们可以访问到子组件的实例，并直接调用子组件的方法 `childFunction()`。

在父组件的 `callChildFunction` 方法中，我们调用了子组件的函数。

通过上述代码，你可以在父组件中调用子组件的函数。在点击父组件中的按钮时，子组件的 `childFunction` 将被调用，并执行相应的操作。请确保在调用子组件函数之前，子组件已经被渲染并在父组件中可用。



#### 子组件调用父子组件的函数

> $emit

要在子组件中调用父组件的函数，你可以使用自定义事件和 `$emit` 方法来实现。以下是一个示例，展示了如何在子组件中调用父组件的函数：

父组件：

``` vue
<template>
  <div>
    <child-component @child-event="parentFunction"></child-component>
  </div>
</template>

<script>
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },
  methods: {
    parentFunction() {
      console.log('父组件函数被调用');
    }
  }
};
</script>
```

在上面的示例中，父组件定义了一个名为 `parentFunction` 的方法，它会在子组件触发 `child-event` 事件时输出一条日志。

子组件：

``` vue
<template>
  <div>
    <button @click="callParentFunction">调用父组件函数</button>
  </div>
</template>

<script>
export default {
  methods: {
    callParentFunction() {
      this.$emit('child-event');
    }
  }
};
</script>
```

在子组件中，当按钮被点击时，`callParentFunction` 方法通过 `this.$emit` 方法触发了一个名为 `child-event` 的自定义事件。

父组件通过在子组件标签上使用 `@child-event` 监听该事件，并将其与 `parentFunction` 方法关联起来。

当子组件触发 `child-event` 事件时，父组件中的 `parentFunction` 方法将被调用。

通过上述代码，你可以在子组件中触发自定义事件来调用父组件的函数。在子组件中点击按钮时，父组件的 `parentFunction` 方法将被调用，并执行相应的操作。



### 监听 watch

在 Vue.js 中，你可以使用 `watch` 来监听数据的变化。`watch` 可以监视一个或多个数据的变化，并在数据变化时执行相应的操作。以下是一个示例，展示了如何使用 `watch` 监听数据的变化：

``` vue
<template>
  <div>
    <p>{{ message }}</p>
    <input v-model="inputText" type="text" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      message: '',
      inputText: ''
    };
  },
  watch: {
    inputText(newValue) {
      this.message = 'Input text has changed: ' + newValue;
    }
  }
};
</script>
```

在上面的示例中，我们在 `data` 中定义了 `message` 和 `inputText` 两个数据属性。

然后，在 `watch` 中定义了一个名为 `inputText` 的 watcher。当 `inputText` 数据发生变化时，watcher 将执行相应的函数。

在函数中，我们将 `message` 更新为 `'Input text has changed: ' + newValue`，其中 `newValue` 是 `inputText` 的新值。

这样，每当输入框的文本发生变化时，`inputText` 数据会被更新，并且 `watch` 中的 watcher 会被触发，进而更新 `message` 的值。

你可以根据需要监听单个数据或多个数据的变化，只需在 `watch` 中定义相应的 watcher，并在函数中处理变化时的逻辑操作。

请注意，`watch` 通常用于监听非响应式的数据或需要进行复杂计算的情况。对于响应式数据的监听，更常用的方法是使用计算属性（computed properties）或侦听器（watchers）来处理。



###  DOM 更新后执行回调函数

> $nextTick

`$nextTick` 是 Vue.js 提供的一个方法，用于在 DOM 更新后执行回调函数。它可以用来确保在获取更新后的 DOM 元素或执行其他操作时，DOM 已经完成了相应的更新。

使用 `$nextTick` 方法的一般形式如下：

```javascript
this.$nextTick(callback);
```

其中，`callback` 是一个回调函数，在 DOM 更新完成后会被调用。

具体的使用场景包括：

1. 获取更新后的 DOM 元素。
2. 执行依赖于 DOM 更新后的操作。
3. 在更新后重新计算或触发某些逻辑。
4. 在更新后使用一些需要 DOM 的插件或库。

下面是一个示例代码，展示了如何使用 `$nextTick` 方法：

```vue
<template>
  <div>
    <p v-if="showMessage">{{ message }}</p>
    <button @click="updateMessage">更新消息</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showMessage: false,
      message: "Hello, Vue!"
    };
  },
  methods: {
    updateMessage() {
      this.showMessage = true;

      // 在下一个微任务中获取更新后的 DOM 元素
      this.$nextTick(() => {
        const messageElement = document.querySelector("p");
        console.log(messageElement.textContent); // 输出更新后的消息内容
      });
    }
  }
};
</script>
```

在这个示例中，点击按钮会触发 `updateMessage` 方法，将 `showMessage` 设置为 `true`，从而显示消息内容。使用 `$nextTick` 方法，确保在获取更新后的 DOM 元素时，DOM 已经完成了相应的更新。

需要注意的是，`$nextTick` 方法可以在组件实例中的任何方法中使用，包括生命周期钩子函数和其他自定义方法。



## vue-router





### 路由跳转





## element-ui









## ruo-yi-ui













