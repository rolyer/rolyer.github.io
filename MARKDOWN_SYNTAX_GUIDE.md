# Jekyll博客Markdown语法指南

本文档总结了本Jekyll博客项目中`_posts`目录下`.md`文件的语法特点和格式规范。

## 项目配置

- **Markdown引擎**: kramdown (在`_config.yml`中配置)
- **代码高亮**: rouge/pygments
- **布局系统**: Jekyll Liquid模板
- **主题**: dbyll

## 1. Front Matter (前置元数据)

每个`.md`文件必须以YAML格式的前置元数据开始和结束：

```yaml
---
layout: post
title: 文章标题
categories: [general, 分类名]
tags: [标签1, 标签2]
description: 文章描述
comments: true
---
```

### 必需字段说明

- `layout`: 固定为 `post`，指定使用`_layouts/post.html`布局
- `title`: 文章标题，会显示在页面顶部和浏览器标题栏
- `categories`: 分类数组，至少包含 `general`，可以有多个分类
- `tags`: 标签数组，用于文章分类和检索
- `description`: 文章描述，用于SEO和文章摘要
- `comments`: 布尔值，控制是否启用Disqus评论系统

### Front Matter示例

```yaml
---
layout: post
title: Memcached安装
categories: [general, Memcached]
tags: [Memcached]
description: Memcached安装介绍
comments: true
---
```

## 2. 文件命名规范

格式：`YYYY-MM-DD-文章标题.md`

**规则**：
- 日期格式：`YYYY-MM-DD`（年-月-日）
- 标题使用连字符`-`分隔单词
- 文件名不区分大小写，但建议使用小写
- 避免使用特殊字符

**示例**：
- `2015-11-25-installed-memcached.md`
- `2025-02-03-Openclaw_Build.md`
- `2016-01-16-react-native-android-application-development-in-windows.md`

## 3. Markdown基础语法

### 标题层级

```markdown
# 一级标题（文章主标题，通常不使用）
## 二级标题（章节标题）
### 三级标题（小节标题）
#### 四级标题（子小节）
```

**注意**：一级标题通常在Front Matter的`title`字段中定义，正文中从二级标题开始。

### 代码块高亮

**重要**：必须使用Jekyll的Liquid语法，而不是标准的Markdown代码块！

```liquid
{% highlight 语言 %}
代码内容
{% endhighlight %}
```

**支持的语言标识符**：
- `bash` / `shell` - Shell命令和脚本
- `javascript` / `js` - JavaScript代码
- `java` - Java代码
- `xml` / `html` - XML/HTML标记
- `python` - Python代码
- `ruby` - Ruby代码
- `css` - CSS样式
- `json` - JSON数据
- 其他rouge支持的所有语言

**示例**：

```liquid
{% highlight bash %}
git clone https://github.com/user/repo.git
git init myproject
{% endhighlight %}
```

```liquid
{% highlight javascript %}
function hello() {
    console.log("Hello, World!");
}
{% endhighlight %}
```

**错误示例**（不要使用标准Markdown代码块）：
```markdown
```bash
git clone url
```
```

### 图片引用

使用Jekyll的站点变量引用图片，确保路径正确：

```markdown
![图片描述文字]({{ site.baseurl }}/assets/media/图片文件名)
```

**示例**：
```markdown
![Git Commands]({{ site.baseurl }}/assets/media/git-commands-show.png)
```

**图片存储位置**：`assets/media/` 目录

### 列表

**无序列表**：
```markdown
* 项目1
* 项目2
  * 子项目2.1
  * 子项目2.2
* 项目3
```

**有序列表**：
```markdown
1. 第一步
2. 第二步
3. 第三步
```

**混合使用**：
```markdown
1. 主要步骤
   * 子步骤1
   * 子步骤2
2. 下一步
```

### 引用块

用于引用重要信息或说明：

```markdown
> 引用内容
> 可以多行
```

**示例**：
```markdown
> 作为一个Java开发人员，有些常用的Linux命令必须掌握。
```

### 文本强调

```markdown
**粗体文本**
*斜体文本*
`行内代码`
```

### 链接

```markdown
[链接文本](URL)
```

**示例**：
```markdown
[GitHub](https://github.com)
[参考文章](http://www.example.com/article)
```

### 水平分割线

```markdown
---
```

用于分隔章节。

## 4. 常见文章结构模式

### 技术教程类文章

**结构**：
1. **前言/介绍** - 说明文章目的和背景
2. **准备工作** - 列出前置条件和所需工具
3. **安装步骤** - 使用有序列表或标题组织
4. **配置说明** - 详细配置步骤
5. **代码示例** - 使用highlight语法展示
6. **错误处理** - 记录常见问题和解决方案
7. **参考资料** - 列出相关链接

**示例文件**：`2015-11-25-installed-memcached.md`

### 命令清单类文章

**结构**：
1. **前言** - 说明命令清单的用途
2. **分类标题** - 使用二级标题 `##` 组织
3. **命令组** - 每组使用highlight代码块
4. **说明文字** - 在代码块前后添加解释
5. **图片来源** - 如果有参考图片

**示例文件**：`2015-12-11-Git-Commands-List.md`

### 配置参考类文章

**结构**：
1. **介绍** - 说明配置文件的作用
2. **配置示例** - 使用highlight展示完整配置
3. **参数说明** - 详细解释各个参数
4. **使用场景** - 说明适用情况

**示例文件**：`2015-12-6-nginx-configuration.md`

## 5. 特殊语法和注意事项

### 代码块中的内容

在highlight代码块中可以包含：
- 命令本身
- 命令输出
- 错误信息
- 配置文件内容
- 代码注释（使用对应语言的注释语法）

**示例**：
```liquid
{% highlight bash %}
# 这是注释
$ git status
# 输出：
# On branch master
# Changes not staged for commit
{% endhighlight %}
```

### 中英文混排

项目中的文章多为中文技术文章，支持中英文混排。注意：
- 中英文之间建议添加空格（可选，根据个人习惯）
- 代码和命令保持英文原样
- 技术术语可以使用英文或中文

### 命令提示符

在bash代码块中可以使用：
- `$` - 表示普通用户命令
- `#` - 表示管理员/root命令
- `[root@hostname ~]#` - 完整的命令提示符（可选）

### 代码注释

在代码块中使用对应语言的注释语法：
- Bash: `# 注释内容`
- JavaScript: `// 注释内容` 或 `/* 注释内容 */`
- HTML: `<!-- 注释内容 -->`

## 6. 实际文件示例分析

### 简单教程类

**文件**：`2015-11-25-installed-memcached.md`

**特点**：
- 使用二级标题组织章节（## 安装步骤、## 参数介绍）
- 每个步骤配合代码块
- 参数说明使用无序列表
- 结构清晰，易于跟随

### 命令清单类

**文件**：`2015-12-11-Git-Commands-List.md`

**特点**：
- 使用二级标题分类（## 一、新建代码库、## 二、配置）
- 大量使用highlight代码块
- 包含图片引用
- 文末包含文章来源链接
- 每个分类都有说明文字

### 综合教程类

**文件**：`2016-01-16-react-native-android-application-development-in-windows.md`

**特点**：
- 多层级标题结构（#、##、###）
- 代码块、图片、引用块混合使用
- 包含错误处理章节（# 错误记录）
- 使用引用块强调重要信息
- 包含截图展示

### HTML格式文件

**注意**：项目中也有`.html`格式的文件（如`2015-10-30-hello-world.html`），这些文件可以直接使用HTML语法，但建议统一使用`.md`格式。

## 7. 最佳实践

1. **Front Matter完整性**
   - 确保所有必需字段都填写
   - 分类和标签要准确
   - description要简洁明了

2. **代码高亮**
   - 始终使用`{% highlight %}`语法
   - 不要使用标准Markdown的代码块（三个反引号）
   - 选择正确的语言标识符

3. **图片路径**
   - 使用`{{ site.baseurl }}`变量
   - 不要硬编码路径
   - 图片放在`assets/media/`目录

4. **标题层级**
   - 保持逻辑清晰的层级结构
   - 不要跳级（如从##直接到####）
   - 一级标题留给Front Matter的title

5. **代码注释**
   - 在代码块中添加必要的注释说明
   - 解释复杂的命令或配置

6. **格式一致性**
   - 保持与现有文章格式一致
   - 使用相同的代码风格
   - 统一使用中文或英文标点

7. **内容组织**
   - 使用清晰的章节结构
   - 重要信息使用引用块强调
   - 长文章添加目录（可选）

8. **可读性**
   - 代码块前后添加说明文字
   - 使用列表组织步骤
   - 适当使用空行分隔段落

## 8. 常见错误避免

1. **不要使用标准Markdown代码块**
   ```markdown
   ❌ ```bash
      git clone url
      ```
   ```

2. **不要硬编码图片路径**
   ```markdown
   ❌ ![img](/assets/media/image.png)
   ✅ ![img]({{ site.baseurl }}/assets/media/image.png)
   ```

3. **不要忘记Front Matter**
   - 每个文件都必须有Front Matter
   - Front Matter前后必须有`---`

4. **不要使用一级标题**
   - 一级标题由Front Matter的title提供
   - 正文从二级标题开始

## 9. 相关文件参考

- **布局文件**: `_layouts/post.html` - 定义文章渲染模板
- **配置文件**: `_config.yml` - Jekyll站点配置
- **Include文件**: `_includes/post.html` - 文章内容渲染逻辑
- **样式文件**: `assets/css/style.css` - 文章样式

## 10. 快速参考模板

创建新文章时，可以使用以下模板：

```markdown
---
layout: post
title: 文章标题
categories: [general, 分类名]
tags: [标签1, 标签2]
description: 文章简短描述
comments: true
---

## 前言

文章介绍和背景说明。

## 主要内容

### 步骤一

说明文字。

{% highlight bash %}
命令或代码
{% endhighlight %}

### 步骤二

更多内容...

## 总结

文章总结。

## 参考资料

- [链接1](URL1)
- [链接2](URL2)
```

---

**最后更新**: 2025-02-03
**维护者**: 参考项目README.md
