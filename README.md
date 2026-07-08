# -# 🌤 天气预报 Web 应用

一个简洁、美观的天气预报单页应用，支持城市搜索、实时天气展示、未来 3 天预报、地理位置定位以及数据持久化。界面采用毛玻璃设计，适配移动端与桌面端。

[在线预览](https://your-username.github.io/weather-forecast/)（将 `your-username` 替换为您的 GitHub 用户名）

## ✨ 功能特性

- **城市搜索**：输入城市名称，按回车或点击搜索按钮查询天气。
- **实时天气**：显示温度、天气状况、湿度、风速、体感温度、紫外线指数。
- **未来预报**：展示未来 3 天的最高/最低温度和天气状况。
- **地理位置定位**：点击定位按钮，通过浏览器 Geolocation API 自动获取当前位置天气（需授予权限）。
- **数据持久化**：使用 `localStorage` 保存最近搜索的城市，刷新页面自动恢复。
- **API Key 管理**：支持配置心知天气（Seniverse）API Key，无 Key 时自动使用模拟数据，开箱即用。
- **响应式设计**：适配手机、平板、桌面等各种屏幕。

## 🛠 技术栈

| 技术 | 用途 |
|------|------|
| **HTML5** | 语义化结构，含 `<header>`、`<section>`、`<dialog>` 等 |
| **CSS3** | 毛玻璃（`backdrop-filter`）、Flexbox/Grid 布局、动画、响应式媒体查询 |
| **JavaScript (ES6+)** | `async/await`、箭头函数、解构赋值、模板字符串、`Promise`、`localStorage` |
| **Fetch / JSONP** | 调用心知天气 API（因跨域限制采用 JSONP） |
| **Geolocation API** | 获取用户当前位置 |
| **localStorage** | 存储 API Key 和最近搜索城市 |

## 📂 代码结构与关键技术说明

### 1. HTML 结构
- 主容器 `.app-container` 承载所有内容，采用毛玻璃效果。
- 搜索区包含输入框、搜索按钮和定位按钮。
- 天气主卡片 `.weather-main` 显示实时天气，初始隐藏。
- 预报区域 `.forecast-section` 动态生成未来 3 天卡片。
- 设置弹窗 `.modal-overlay` 用于配置 API Key。

### 2. CSS 响应式设计
- 使用 `flex-wrap` 和 `grid-template-columns: repeat(auto-fit, minmax(100px, 1fr))` 实现自适应。
- 媒体查询针对 `max-width: 500px` 和 `380px` 调整字体、间距和列数。
- 毛玻璃效果：`background: rgba(255,255,255,0.12); backdrop-filter: blur(20px);`

### 3. JavaScript 核心逻辑

#### (1) API 请求（JSONP 跨域）
```javascript
// 通过动态创建 <script> 标签实现 JSONP 调用
const script = document.createElement('script');
const callbackName = 'weather_callback_' + Date.now();
window[callbackName] = function(data) { /* 处理数据 */ };
script.src = `https://api.seniverse.com/v3/weather/now.json?key=${key}&location=${city}&callback=${callbackName}`;
document.head.appendChild(script);
 
function parseWeatherData(data) {
  const result = data.results[0];
  return {
    city: result.location.name,
    now: { temp: result.now.temperature, text: result.now.text, ... },
    daily: result.daily.slice(0, 3).map(d => ({ high: d.high, low: d.low, ... }))
  };
}

navigator.geolocation.getCurrentPosition(position => {
  const { latitude, longitude } = position.coords;
  // 调用心知天气 location/search 接口将经纬度转为城市名
});


// 保存
localStorage.setItem('weather_app_data', JSON.stringify({ apiKey, lastCity }));
// 读取
const data = JSON.parse(localStorage.getItem('weather_app_data'));
