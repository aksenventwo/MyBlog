## 关于Technical Docs

文档使用`MKDocs`构建，快速好用，五星好评！

## MKDocs 常用语法

### 多级文档

可以通过在章节标题下将相关页面列在一起来创建子节。 例如:

```
pages:
- Home: 'index.md'
- User Guide:
    - 'Writing your docs': 'user-guide/writing-your-docs.md'
    - 'Styling your docs': 'user-guide/styling-your-docs.md'
- About:
    - 'License': 'about/license.md'
    - 'Release Notes': 'about/release-notes.md'
```

### 链接

```
Please see the [project license](license.md) for further details.
```

```
Please see the [project license](../about/license.md) for further details.
```

```
Please see the [project license](about.md#license) for further details.
```

### 图片和音频

```
mkdocs.yml
docs/
    CNAME
    index.md
    about.md
    license.md
    img/
        screenshot.png
```

```
Cupcake indexer is a snazzy new project for indexing small cakes.

![Screenshot](img/screenshot.png)

*Above: Cupcake indexer in progress*
```

### Markdown 扩展

这里将不定期更新`Markdown`的使用。

### 表格

一个简单的表格:

```
First Header | Second Header | Third Header
------------ | ------------- | ------------
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell
```

如果您愿意，可以在表格的每一行添加一个前导和尾随`|`:

```
| First Header | Second Header | Third Header |
| ------------ | ------------- | ------------ |
| Content Cell | Content Cell  | Content Cell |
| Content Cell | Content Cell  | Content Cell |
```

通过向分隔线添加冒号为每列指定对齐方式:

```
First Header | Second Header | Third Header
:----------- |:-------------:| -----------:
Left         | Center        | Right
Left         | Center        | Right
```

### 控制代码块

代码引用

```
Fenced code blocks are like Standard
Markdown’s regular code blocks, except that
they’re not indented and instead rely on
start and end fence lines to delimit the
code block.
```

语法高亮

```python
def hello():
    print("Hello World!")
```