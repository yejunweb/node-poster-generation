# Node Poster Generation

一个基于 Node.js 和 Puppeteer 的海报生成工具，可以将 HTML 模板转换为高质量的 JPG 图片。

## 功能特性

-   🎨 支持自定义 HTML 模板
-   🔄 模板变量替换
-   📝 条件语句支持（`{{#if}}`、`{{#ifCond}}`）
-   💰 内置价格格式化函数（`{{formatPrice}}`）
-   🖼️ 高质量图片输出（JPEG，90% 质量）
-   📱 预设移动端尺寸（750x1334）

## 安装

```bash
npm install
```

## 依赖

-   **puppeteer**: ^24.34.0 - 用于无头浏览器渲染和截图

## 使用方法

### 基本用法

```javascript
const PosterGenerator = require('./app.js')

const posterGenerator = new PosterGenerator()

// 初始化浏览器
await posterGenerator.init()

// 生成海报
const data = {
    price: 268,
    area: 89,
    gift: 15,
    layout: '3室2厅',
    decoration: '精装',
    hasParking: true,
    parkingPrice: 25,
    extraArea: 12,
    floor: '15/30',
    taxFree: 'true',
}

await posterGenerator.generate('house-poster', data)

// 关闭浏览器
await posterGenerator.close()
```

### 自定义输出文件名

```javascript
await posterGenerator.generate('house-poster', data, 'my-poster.jpg')
```

## 模板语法

### 变量替换

使用双大括号语法替换变量：

```html
<div>总价：{{price}}万</div>
<div>面积：{{area}}平</div>
```

### 价格格式化

使用 `formatPrice` 函数格式化价格（自动添加千分位分隔符）：

```html
<div>总价：{{formatPrice price}}万</div>
```

输入：`price: 268`  
输出：`268万`

输入：`price: 1234`  
输出：`1,234万`

### 条件语句

#### 简单条件判断

```html
{{#if hasParking}}
<div>带车位：{{parkingPrice}}万</div>
{{/if}}
```

#### 条件比较

```html
{{#ifCond taxFree '==' 'true'}}
<div>无增值税</div>
{{/ifCond}}
```

支持的比较运算符：

-   `'=='` 或 `'==='` - 等于
-   `'!='` 或 `'!=='` - 不等于

## 项目结构

```
node-poster-generation/
├── app.js                    # 主程序文件
├── package.json              # 项目配置
├── templates/                # 模板目录
│   └── house-poster.html    # 房源海报模板
└── output/                   # 输出目录（自动生成）
    └── *.jpg                # 生成的图片文件
```

## API 文档

### `PosterGenerator` 类

#### 构造函数

```javascript
const generator = new PosterGenerator()
```

#### 方法

##### `init()`

初始化 Puppeteer 浏览器实例。

```javascript
await generator.init()
```

##### `generate(templateName, data, outputFileName)`

生成海报图片。

**参数：**

-   `templateName` (string): 模板文件名（不含 .html 扩展名）
-   `data` (object): 模板数据对象
-   `outputFileName` (string, 可选): 输出文件名，默认为 `{templateName}-{timestamp}.jpg`

**返回：**

-   `Promise<string>`: 输出文件的完整路径

**示例：**

```javascript
const outputPath = await generator.generate('house-poster', {
    price: 268,
    area: 89,
})
console.log(`图片已保存到: ${outputPath}`)
```

##### `close()`

关闭浏览器实例。

```javascript
await generator.close()
```

## 配置

### 视口尺寸

默认视口尺寸为 750x1334（移动端尺寸），可在 `app.js` 中修改：

```javascript
await page.setViewport({ width: 750, height: 1334 })
```

### 图片质量

默认 JPEG 质量为 90%，可在 `app.js` 中修改：

```javascript
const screenshot = await page.screenshot({
    type: 'jpeg',
    quality: 90, // 修改此值（0-100）
    fullPage: false,
})
```

## 示例数据

```javascript
const mockHouseData = {
    price: 268, // 总价（万元）
    area: 89, // 面积（平米）
    gift: 15, // 赠送面积（平米）
    layout: '3室2厅', // 户型
    decoration: '精装', // 装修
    hasParking: true, // 是否有车位
    parkingPrice: 25, // 车位价格（万元）
    extraArea: 12, // 附加面积（平米）
    floor: '15/30', // 楼层
    taxFree: 'true', // 是否免税
}
```

## 运行示例

直接运行 `app.js` 会使用示例数据生成一张房源海报：

```bash
node app.js
```

生成的图片会保存在 `output/` 目录下。

## 注意事项

1. 首次运行 Puppeteer 会自动下载 Chromium 浏览器，可能需要一些时间
2. 确保有足够的磁盘空间保存生成的图片
3. 模板文件必须放在 `templates/` 目录下
4. 输出目录 `output/` 会在首次生成时自动创建

## 许可证

ISC
