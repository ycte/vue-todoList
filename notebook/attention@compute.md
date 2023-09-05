> Vue 3 只对响应式的数据进行更新

#### computed 作为组件数据来源时出现的问题

###### 问题1：组件无法根据计算属性的值响应式更新

```js
// 响应式数据定义
const todos = ref([
  {
    id: 0,
    title: "吃饭",
    completed: true,
  },
]); // 所有备忘列表
const newTodo = ref(""); // 输入框输入的内容

// 计算属性定义
const matchTodos = computed(() => {
  return todos.value.filter((todo) => {
    return todo.title.toLowerCase()
      .indexOf(newTodo.value.toLowerCase()) > -1;
  });
}); // 与当前输入匹配的todo

// 对计算属性的调用
<li
  v-for="todo in matchTodos"
  class="todo"
  :key="todo.id"
  :class="{ completed: todo.completed }"
>
  ...
</li>
```

###### 解决：理解错误，是在使用 `<transition-group>` 动画组时，没有定义动画的事件所调用的函数导致元素没有迭代。



> 这里需要深度理解 `<transition-group>`

##### 问题1进一步探讨

###### 列表交错过渡

交错过渡听起来好厉害的样子，其实说白了就是：设置延时，给元素设置顺序延时的动画效果，通过 data 属性与 JavaScript 通信就可以实现啦。大概就是获取元素的序号，计算延时，然后设置定时器动画。

###### 出现问题的过渡动画

``` js
/* 动画效果 */
// 进入中
function beforeEnter(el) {
  // el.style.opacity = 0;
  // el.style.height = 0;
}
function enter(el, done) {
  // 设置延时
  var delay = el.dataset.index * 150;
  /* setTimeout(function () {
    // 更新元素样式
    Velocity(el, { opacity: 1, height: "58px" }, { complete: done });
  }, delay); */
}
// 离开时
function leave(el, done) {
  // 设置延时
  var delay = el.dataset.index * 150;
  /* setTimeout(function () {
    // 更新元素样式
    Velocity(el, { opacity: 0, height: 0 }, { complete: done });
  }, delay); */
}
```

###### 解析

* 错误操作：上述代码中，在动画的每个钩子函数位置，将样式更改全部注释。

* 具体效果：只增不减，新增元素按照 id 排序

  问题：增加时重新排列好理解，减少时不减？

  * vue 动画钩子

    ``` js
    // ...
    methods: {
      // --------
      // 进入中
      // --------
    
      beforeEnter: function (el) {
        // ...
      },
      // 此回调函数是可选项的设置
      // 与 CSS 结合时使用
      enter: function (el, done) {
        // ...
        done()
      },
      afterEnter: function (el) {
        // ...
      },
      enterCancelled: function (el) {
        // ...
      },
    
      // --------
      // 离开时
      // --------
    
      beforeLeave: function (el) {
        // ...
      },
      // 此回调函数是可选项的设置
      // 与 CSS 结合时使用
      leave: function (el, done) {
        // ...
        done()
      },
      afterLeave: function (el) {
        // ...
      },
      // leaveCancelled 只用于 v-show 中
      leaveCancelled: function (el) {
        // ...
      }
    }
    ```

  * 解析

    * enter 控制新增列表元素：
    * leave 控制删除列表元素：
    * 两个钩子调用的方法分别控制 `capatity` 和 `height` , 即透明度和高度，注释掉无法将高度和透明度设为0，自然只增不减。
