---
title: 从D2-admin框架中Vue学习项目
date: 2020-01-30 14:46:07
tags:
	- vue
	- 前端
---



## 主页页面

```html
<template>
  <d2-container class="page">
    <!-- cover组件 -->
    <d2-page-cover>
      <d2-icon-svg class="logo" name="d2-admin"/>
      <!-- 定义一个模板名为footer  -->
      <template slot="footer">
				.....
      </template>
    </d2-page-cover>
  </d2-container>
</template>
<!-- 
1. page用了d2-page-cover标签
2. d2-page-cover标签 里面有个 <template slot="footer">
3. d2-page-cover源码里面有个 <slot name="footer"/>
总结: <template slot="footer"> 里面的内容将会在 <slot/> 后显示
--> 
```



```html
<template>
  <div class="d2-page-cover">
    ...
	<!--  <template slot="footer"> 里面的内容将会在` <slot/> `后显示,即是这里 -->
    <slot name="footer"/>
    <a target="blank" href="https://github.com/d2-projects/d2-admin">
      <img
        style="position: absolute; top: 0; right: 0; border: 0; width: 150px;"
        src="./image/darkblue@2x.png"
        alt="Fork me on GitHub">
    </a>
  </div>
</template>
```



### Slot 插槽

1. page用了d2-page-cover标签
2. d2-page-cover标签 里面有个`<template slot="footer">`
3. d2-page-cover源码里面有个` <slot name="footer"/>`



**总结: `<template slot="footer"> `里面的内容将会在` <slot name="footer"/> `后显示**



<!-- more -->

## 添加路由页面

```javascript
<script>
import { cloneDeep } from 'lodash'
import { mapState, mapMutations } from 'vuex'
import { frameInRoutes } from '@/router/routes'
import layoutHeaderAside from '@/layout/header-aside'
export default {
  data () {
    return {
      title: '',
      setting: [
        { title: '追加页面 1', name: 'add-routes-1', path: 'add-routes/1', component: '1' },
        { title: '追加页面 2', name: 'add-routes-2', path: 'add-routes/2', component: '2' },
        { title: '追加页面 3', name: 'add-routes-3', path: 'add-routes/3', component: '3' }
      ]
    }
  },
  computed: {
    // 1. 知识点 ...mapState
    ...mapState('d2admin/menu', [
      'header'
    ]),
    dataView () {
      return JSON.stringify(this.setting, null, 2)
    }
  },
  methods: {
    // 2. 知识点 ...mapMutations
    ...mapMutations({
      pageInit: 'd2admin/page/init',
      headerSet: 'd2admin/menu/headerSet'
    }),
    onClick ({ title, name, path, component }) {
      const route = [
        {
          path: '/demo/playground',
          component: layoutHeaderAside,
          children: [
            {
              path,
              name,
              component: () => import(`@/views/demo/playground/add-routes/alternates/${component}.vue`),
              meta: {
                title
              }
            }
          ]
        }
      ]
      this.$router.addRoutes(route)
      // 更新标签页池
      this.pageInit([
        ...frameInRoutes,
        ...route
      ])
      // 2. 添加一个菜单组
      let menuGroup = {
        title: '临时菜单',
        icon: 'plus-square',
        children: []
      }
      // 3. 添加一个菜单内容
      const menu = {
        path: `/demo/playground/${path}`,
        title,
        icon: 'file-o'
      }
      // 4.获取目前的菜单
      let header = cloneDeep(this.header)
      const menuGroupIndex = header.findIndex(e => e.title === menuGroup.title)
      // 如果顶栏菜单已经有这个组，就在组里添加项目，反之新建一个菜单组
      if (menuGroupIndex >= 0) {
        header[menuGroupIndex].children.push(menu)
      } else {
        menuGroup.children.push(menu)
        header.push(menuGroup)
      }
      // 设置菜单头
      this.headerSet(header)
      // 跳转
      this.$router.push({ name })
    }
  }
}
</script>
```

### VUEX



#### ...mapXXX

1. `...mapState('d2admin/menu', ['header']),` 意思是获取 store下的 d2admin/menu 模块下的 header 变量 这里存储这 菜单头的内容

2. 

   ```javascript
   ...mapMutations({
     pageInit: 'd2admin/page/init',
     headerSet: 'd2admin/menu/headerSet'
   }),
   ```
   意思是获取 d2admin/page 下的init 方法 和 d2admin/menu 模块下的 headerSet方法 **注意这些方法是可以改变 State的<u>同步</u>方法**
   
   

**总结：目的是操作VUEX，好处是可以将这些方法通过 this.XXX 调用出来** 





## D2-crud

### `$refs` 和 `$emit`

```html
<d2-crud
  ref="d2Crud"  																							1.知识点"ref"
  add-title="我的新增"
  :row-handle="rowHandle"
  :columns="columns"
  :data="data"
  :loading="loading"
  :pagination="pagination"
  :edit-template="editTemplate"
  selection-row
  @selection-change="handleSelectionChange"
  @dialog-cancel="handleDialogCancel"
  @row-add="handleRowAdd"
  @row-edit="handleRowEdit"
  @pagination-current-change="paginationCurrentChange"
  @cell-data-change="handleCellDataChange"                    2.知识点"$emit"
  @row-remove="handleRowRemove">

</d2-crud>

<script>
    editRowWithNewTemplate () {
      // 可以直接操作ref="d2Crud"组件的showDialog方法 
      this.$refs.d2Crud.showDialog({
        mode: 'edit',
        rowIndex: 2,
        template: {
          date: {
            title: '日期',
            value: ''
          },
          name: {
            title: '姓名',
            value: ''
          }
        }
      })
    },
</script>
```



查看 D2-crud 里面的一个组件

```html
<el-switch
  v-else-if="handleFormTemplateMode(key).component.name === 'el-switch'"
  v-model="formData[key]"
  v-bind="handleFormTemplateMode(key).component"
  @change="$emit('form-data-change', {key: key, value: value})" 调用了$emit
>
```



1. 使用 ref="d2Crud" 可以对组件引用并且直接操作组件方法

2. $emit 发布事件 由父组件接受处理