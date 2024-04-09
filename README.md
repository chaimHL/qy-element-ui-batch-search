# 批量搜索组件

可一次性输入多个搜索条件，使用空格或换行符区分。

# 使用

在使用了 element-ui 的 vue2 项目中，在 src\main.js 引入并使用 Vue.use() 来全局安装组件：

```javascript
import QyElementUiBatchSearch from 'qy-element-ui-batch-search'
Vue.use(QyElementUiBatchSearch)
```

在 .vue 文件中，即可直接使用：

```vue
<el-form-item label="运单号">
  <QyElementUiBatchSearch
    ref="qyElementUiBatchSearchRef"
    @change-input="onChangeInput('trackingNumberList', $event)"
    @clear-input="onClearInput('trackingNumberList')"
  />
</el-form-item>
```
