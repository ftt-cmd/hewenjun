# 项目规则

## 命名规则
- 所有 CSS 类名必须以 `hwj_` 开头（例如：`hwj_container`、`hwj_header`）
- 所有 id 名必须以 `hwj_` 开头（例如：`hwj_main`、`hwj_nav`）
- 所有 JS 变量名必须以 `hwj_` 开头（例如：`hwj_data`、`hwj_config`）

## 页面注释规则
- 所有 HTML 页面中必须包含注释，注释内容格式：`姓名：袁磊，日期：功能名称`

## 页面链接规则
- 新建的 HTML 文件必须包含返回首页 `index.html` 的超链接

## 技术栈规则
- 项目使用原生的 HTML、CSS 和 JavaScript 实现
- 图标使用 `font` 目录下的 iconfont 图标
- 不使用第三方库和 CDN
- 使用 fetch 获取服务器端数据

## 提示词记录规则
- 在执行任何任务之前，必须先将提示词写入 `yuan_prompt.md` 文件
- 必须以附加（append）方式写入，绝不覆盖之前的内容
- 每次写入的内容必须包含：
  - 时间：格式 `yyyy-mm-dd-hh-mm`
  - 使用的模型
  - 提示词内容
