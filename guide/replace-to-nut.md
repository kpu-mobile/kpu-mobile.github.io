# 替换为 NutUI

由于框架默认使用的是 Vant 组件库，并且演示源码中大量示例也使用了 Vant，如果你需要使用 [NutUI](https://nutui.jd.com/h5/vue/4x)，请拉取框架源码分支，或者到 [Github Releases](https://github.com/kpu-mobile/web/releases) 页面下载框架源码压缩包。

## 安装

```sh
# 安装依赖
pnpm install

# 安装 NutUI
pnpm add @nutui/nutui @nutui/touch-emulator
```

## 代码调整

::: details 代码

整体修改 `/src/ui/provider/index.ts` 文件

```ts
import type { App } from 'vue'
import NutUI from '@nutui/nutui'
import enUS from '@nutui/nutui/dist/packages/locale/lang/en-US'
import zhCN from '@nutui/nutui/dist/packages/locale/lang/zh-CN'
import '@nutui/nutui/dist/style.css'
import '@nutui/touch-emulator'

function install(app: App) {
  app.use(NutUI)
}

// 此处的对象属性和 src/locales/index.ts 中的 messages 对象属性一一对应
const locales: Record<string, any> = {
  'zh-cn': zhCN,
  'en-us': enUS,
}

export default { install }
export { locales }
```

整体修改 `/src/ui/provider/index.vue` 文件

```vue
<script setup lang="ts">
import useSettingsStore from '@/store/modules/settings'
import { Locale } from 'vant'
import { locales } from './index'

const settingsStore = useSettingsStore()

watch(() => settingsStore.lang, () => {
  Locale.use(settingsStore.lang, locales[settingsStore.lang])
})
</script>

<template>
  <NutConfigProvider :theme="settingsStore.currentColorScheme" class="min-h-vh supports-[(min-height:100dvh)]:min-h-dvh">
    <slot />
  </NutConfigProvider>
</template>
```

:::

## 卸载

```sh
# 卸载 Vant
pnpm remove vant @vant/touch-emulator
```

## 完成

至此，你已经将框架中的 Vant 组件库替换为 NutUI 组件库，并且可以开始使用 NutUI 进行业务开发了。

## 示例

如果对上述的步骤还有不清楚的地方，可以访问[此仓库](https://github.com/kpu-mobile/nut-example)查看示例源码，以及[此链接](https://kpu-mobile.kpui.top/nut-example/)查看示例网站。

![](/ui-nut.png){data-zoomable}
