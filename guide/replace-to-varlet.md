# 替换为 Varlet

由于框架默认使用的是 Vant 组件库，并且演示源码中大量示例也使用了 Vant，如果你需要使用 [Varlet](https://varlet.pages.dev)，请拉取框架源码分支，或者到 [Github Releases](https://github.com/kpu-mobile/web/releases) 页面下载框架源码压缩包。


## 安装

```sh
# 安装依赖
pnpm install

# 安装 NutUI
pnpm add @varlet/ui @varlet/touch-emulator
```

## 代码调整

::: details 代码

整体修改 `/src/ui/provider/index.ts` 文件

```ts
import type { App } from 'vue'
import Varlet, { Locale } from '@varlet/ui'
import enUS from 'vant/es/locale/lang/en-US'
import zhCN from 'vant/es/locale/lang/zh-CN'
import '@varlet/ui/es/style'
import '@varlet/touch-emulator'

function install(app: App) {
  app.use(Varlet)
}

// 此处的对象属性和 src/locales/index.ts 中的 messages 对象属性一一对应
const locales: Record<string, any> = {
  'zh-cn': Locale.zhCN,
  'en-us': Locale.enUS,
}

export default { install }
export { locales }
```

整体修改 `/src/ui/provider/index.vue` 文件

```vue
<script setup lang="ts">
import useSettingsStore from '@/store/modules/settings'
import { locales } from './index'

const settingsStore = useSettingsStore()

watch(() => settingsStore.currentColorScheme, (val) => {
  if (val === 'light') {
    StyleProvider(null)
  }
  else {
    StyleProvider(Themes.dark)
  }
})
</script>

<template>
  <VarLocaleProvider :locale="settingsStore.lang" :messages="locales[settingsStore.lang]" class="min-h-vh supports-[(min-height:100dvh)]:min-h-dvh">
    <slot />
  </VarLocaleProvider>
</template>
```

:::

## 卸载

```sh
# 卸载 Vant
pnpm remove vant @vant/touch-emulator
```

## 完成

至此，你已经将框架中的 Vant 组件库替换为 Varlet 组件库，并且可以开始使用 Varlet 进行业务开发了。

## 示例

如果对上述的步骤还有不清楚的地方，可以访问[此仓库](https://github.com/kpu-mobile/varlet-example)查看示例源码，以及[此链接](https://kpu-mobile.kpui.top/varlet-example/)查看示例网站。

![](/ui-varlet.png){data-zoomable}
