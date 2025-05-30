# 权限

在应用配置中设置：

```ts {2-8}
const globalSettings: Settings.all = {
  app: {
    /**
     * 是否开启权限功能
     * @默认值 `false`
     */
    enablePermission: true,
  },
}
```

然后在 `/src/api/modules/user.ts` 文件里找到 `getPermissions` 的方法，该方法用于登录成功后获取用户权限。在实际开发中，需要手动进行修改，框架默认通过 mock 模拟获取用户权限。

在演示源码中，默认提供了两组权限，你可以在“权限验证”导航里切换帐号查看不同权限下的效果。

## 路由权限

在路由的 `meta` 配置项中，其中有一个 `auth` 参数，这个就是用来配置路由的权限，一个路由可以配置多个权限，当配置多个权限时，只要满足其中任何一个，则视为用户有访问该路由的权限，如下：

```ts
meta: {
  auth: ['news.browse', 'news.edit'],
},
```

框架内部鉴权的逻辑是字符串比对，所以建议对权限有个统一的格式，例如为 `xxx.yyy` ，其中 `xxx` 表示模块名， `yyy` 表示操作类型。那么上面那个例子就表示：

- `news.browse` 新闻模块的浏览权限
- `news.edit` 新闻模块的编辑权限

路由权限是比较暴力的，即没有权限则该路由页面无法访问，并且也不会在导航栏中显示。但在实际业务中，遇到更多的情况通常是，可以访问路由页面，但会根据不同权限，使用页面里的不同功能，这时候就需要用到下面三种鉴权方式。

## 鉴权指令

对于单个元素，提供了 `v-auth` 指令。

```vue-html
<!-- 单权限验证 -->
<button v-auth="'department.create'">新增部门</button>

<!-- 多权限验证，用户只要具备其中任何一个权限，则验证通过 -->
<button v-auth="['department.create', 'department.edit']">新增部门</button>

<!-- 多权限验证，用户必须具备全部权限，才验证通过 -->
<button v-auth.all="['department.create', 'department.edit']">新增部门</button>
```

## 鉴权组件

页面中某个模块，当前用户具备该权限是如何显示，不具备该权限又是如何显示，针对这样的需求，框架提供了 `<KmAuth>` 组件。

```vue-html
<!-- 单权限验证 -->
<KmAuth :value="'department.create'">
  <p>你有该权限</p>
  <template #no-auth>
    <p>你没有该权限</p>
  </template>
</KmAuth>

<!-- 多权限验证，用户只要具备其中任何一个权限，则验证通过 -->
<KmAuth :value="['department.create', 'department.edit']">
  <p>你有该权限</p>
  <template #no-auth>
    <p>你没有该权限</p>
  </template>
</KmAuth>

<!-- 多权限验证，用户必须具备全部权限，才验证通过 -->
<KmAuth :value="['department.create', 'department.edit']" all>
  <p>你有该权限</p>
  <template #no-auth>
    <p>你没有该权限</p>
  </template>
</KmAuth>
```

## 鉴权函数

鉴权组件和鉴权指令控制的是页面上的元素，而鉴权函数则更多是使用在业务流程代码里的权限判断。

```ts
import useAuth from '@/utils/composables/useAuth'
const { auth, authAll } = useAuth()

// 单权限验证，返回 true 或 false
auth('department.create')

// 多权限验证，用户只要具备其中任何一个权限，则验证通过，返回 true 或 false
auth(['department.create', 'department.edit'])

// 多权限验证，用户必须具备全部权限，才验证通过，返回 true 或 false
authAll(['department.create', 'department.edit'])
```

## 小技巧

由于权限配置不涉及角色，所以在实现上会更灵活，开发者可自行扩展出角色层，根据不同角色动态设置该角色所拥有的权限，然后用户与角色挂钩，这样就无需繁琐的给每个用户重复分配权限。

当然了，业务有大有小，针对一些简单场景，对权限没有这么多复杂的要求，也可以将角色直接配置到 `auth` 里。

```vue
<script setup lang="ts">
definePage({
  meta: {
    auth: 'admin'
  },
})
</script>

<template>
  <!-- ... -->
</template>
```

如上所示，如果用户是 `admin` 权限，则可以访问该路由。
