
| 指令 | 描述 | 用例 |
| :------------------------------: | :--: | :--: |
| v-bind | 将这个元素节点的 `title` 特性和 Vue 实例的 `message` 属性保持一致 | `<span v-bind:title="message"> ` |
| v-if | 是否显示指定元素 | ` <p v-if="seen">现在你看到我了</p>` |
| v-for | 循环遍历某个元素并绑定 | `<ol>     <li v-for="todo in todos">       {{ todo.text }}     </li>   </ol>` |
| v-on | 令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法 | ` <button v-on:click="reverseMessage">逆转消息</button>` |
|                                  |      |      |

