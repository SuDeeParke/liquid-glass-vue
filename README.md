# liquid-glass-vue

一个基于 Vue 实现液态玻璃效果的 demo. 灵感来源于 [liquid-glass-react](https://github.com/rdev/liquid-glass-react)。

## 🌟 功能特点

- 使用 Vue 实现液态玻璃效果
- 支持动态交互，模拟液体流动
- 轻量级实现，易于集成到其他项目中

## 🛠 安装与运行

1. **克隆仓库**

   ```bash
   git clone https://github.com/your-username/liquid-glass-vue.git
   cd liquid-glass-vue
   ```

2. **安装依赖**

   ```bash
   npm install
   ```

3. **启动开发服务器**

   ```bash
   npm run dev
   ```

## 📦 使用方法

1. 引入组件：

   ```vue
   <script setup>
   import LiquidSVG from './components/liquidSVG/index.vue'
   </script>

   <template>
   	<div id="app">
   		<liquidSVG />
   	</div>
   </template>
   ```

2. 根据需要自定义样式和动画参数，例如调整颜色、透明度或流速。

## 🤝 贡献指南

欢迎提交 issue 和 PR！如果你希望添加新功能或改进性能，请先创建 issue 讨论需求，然后 fork 本仓库并提交你的修改。

## 📄 许可证

本项目基于 MIT License 协议，详情请查看 [LICENSE](./LICENSE) 文件。
