# AI 协作规范

本文件约束所有 AI 工具在本仓库中生成、修改代码与文档时的行为。AI 在开始工作前应先阅读本文件，与本文件冲突的其他习惯一律以本文件为准。

## 一、项目定位

- 项目名称：小说辅助系统（前端）
- 技术栈：Vue 3 + Vite，TypeScript 优先
- 本仓库仅存放前端代码，后端代码不入本仓库

## 二、代码格式规范

### 基础格式

- 文件编码统一 UTF-8（无 BOM），换行符 LF，缩进 2 个空格，禁止 Tab
- 单行长度不超过 120 字符，超出时换行
- 所有代码块、语句必须以分号结尾（跟随项目 ESLint/Prettier 配置，配置就位前遵循本条）

### 命名规范

| 对象 | 规范 | 示例 |
| --- | --- | --- |
| Vue 组件文件 | PascalCase | `NovelEditor.vue` |
| 其他 js/ts 文件 | camelCase | `novelApi.ts` |
| 变量、函数 | camelCase | `getChapterList()` |
| 类型、接口 | PascalCase | `ChapterInfo` |
| 常量 | UPPER_SNAKE_CASE | `MAX_CHAPTER_COUNT` |
| CSS 类名 | BEM | `chapter-list__item--active` |

### Vue / TypeScript 约定

- 组件一律使用 Composition API + `<script setup lang="ts">`，不使用 Options API
- 组件导出名与文件名保持一致
- import 顺序：Node 内置模块 → 第三方库 → `@/` 内部模块 → 类型 → 样式文件
- 组件模板中单个标签属性超过 3 个时逐属性换行
- 禁止使用 `any`；确实无法确定类型时用 `unknown` 并收窄

### 注释规范

- 业务逻辑注释使用中文，说明"为什么这样做"而非"这行在做什么"
- 导出函数、组合式函数使用 JSDoc 风格注明参数与返回值
- 调试用的 `console.log` 在提交前必须删除

## 三、文件存储规范

### 目录结构

```
├── src/
│   ├── api/          # 接口请求模块，按业务域拆分（如 novel.ts、user.ts）
│   ├── assets/       # 静态资源（图片、字体等）
│   ├── components/   # 公共组件（跨页面复用）
│   ├── composables/  # 组合式函数（useXxx）
│   ├── router/       # 路由配置
│   ├── stores/       # Pinia 状态仓库
│   ├── styles/       # 全局样式
│   ├── types/        # TypeScript 类型定义
│   ├── utils/        # 纯工具函数（无业务语义）
│   └── views/        # 页面级组件，页面私有组件放在对应页面子目录下
├── docs/             # 项目文档、规范类 Markdown
├── public/           # 不经过构建处理的静态文件
├── .env.example      # 环境变量示例（仅变量名 + 占位值）
├── .gitignore
├── AGENTS.md         # 本文件
└── README.md
```

### 存放规则

- 新文件必须放入上表对应目录，禁止在仓库根目录散落源码文件
- 单文件超过 300 行时考虑拆分
- 公共组件放 `src/components/`；仅单个页面使用的组件放该页面目录下
- 文档类 Markdown 一律放 `docs/`，根目录只保留 `README.md`、`AGENTS.md` 等入口文件
- 路径别名统一使用 `@/` 指向 `src/`，禁止 `../` 相对路径跨目录引用
- 图片等二进制资源超过 500KB 时不入库，说明存放方案并经用户确认

### Git 提交规范

- 提交信息格式：`<type>: <中文描述>`，type 取值：`feat` / `fix` / `docs` / `style` / `refactor` / `test` / `chore`
- 一次提交只做一件事，禁止把不相关改动混入同一提交
- 禁止提交 `.gitignore` 忽略名单中的文件（`node_modules/`、`dist/`、`.env` 等）

## 四、敏感内容规范

### 凭据与密钥

- 禁止在代码、注释、文档、示例、配置中写入任何真实凭据，包括：API Key、Token、JWT、密码、账号、数据库连接串、第三方服务密钥、内部服务器地址
- 所有环境相关配置通过 `.env.*` 文件读取，代码中使用 `import.meta.env.VITE_XXX` 访问
- `.env` 已被 git 忽略不入库；需要说明配置项时提供 `.env.example`，只含变量名和明显占位值（如 `your-api-key-here`）
- 生成示例/测试数据时使用明显占位符，不得使用看似真实的凭据或真实用户数据

### 用户内容隐私

- 用户的小说正文、大纲、设定等创作内容属于隐私数据：
  - 前端日志不得输出完整正文与用户个人信息
  - 未经用户明确同意，不得将上述内容发送到本项目后端以外的任何第三方服务
- 错误上报、埋点等如需采集内容，必须先脱敏（截断、掩码）

### 提交前检查

- 每次提交前必须检查 `git status` 与 `git diff --staged`，确认无敏感文件、敏感字符串混入
- 发现历史提交中包含敏感信息时，立即报告用户并给出处理建议，不得自行改写历史或强制推送

## 五、AI 行为约束

- 不主动执行 `push`、`reset --hard`、`force push` 等外发或破坏性 Git 操作，须用户明确指示
- 修改 `.gitignore`、CI 配置、依赖版本前，先向用户说明影响范围
- 引入新的第三方依赖前，说明包名、用途与体积影响，经用户确认后再安装
- 存放位置或命名不确定时：本文件优先，其次参考仓库内现有代码风格
- 本文件内容需要更新时，修改后须提醒用户 review
