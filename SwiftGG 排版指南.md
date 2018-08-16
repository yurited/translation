# 空格

- 中英文之间需要增加空格
  ```patch
  + 我们是 SwiftGG 翻译组
  - 我们是SwiftGG翻译组
  - 我们是SwiftGG 翻译组
  - 我们是 SwiftGG翻译组
  ```
  
  ❗️注意：无论英文是否是一个链接（`[]()`）还是某种变量（使用 ``` ` ` ``` 引起来的内容），遇到中文都应该前后增加空格。
  
   ```patch
   + [这是一个链接](github.com/Cee)。也可以通过访问 [Swift Evolution](https://github.com/apple/swift-evolution) 获得更多详细的解释。
   - [这是一个链接](github.com/Cee)。也可以通过访问[Swift Evolution](https://github.com/apple/swift-evolution)获得更多详细的解释。
   ```

- 中文与数字之间需要增加空格
  ```patch
  + 这篇文章有 3 个小节
  - 这篇文章有3个小节
  - 这篇文章有3 个小节
  - 这篇文章有 3个小节
  ```

- 数字与单位之间需要增加空格
  ```patch
  + 文件大小是 1 MB
  - 文件大小是 1MB
  ```

- 全角标点（特指中文标点）与其他字符之间不加空格
  ```patch
  + 我的 GitHub 帐号是 @Cee，其他的联系方式可以私信。
  - 我的 GitHub 帐号是 @Cee ，其他的联系方式可以私信。
  ```
  
# 标点

- 全角和半角
  - 在中文的句子中使用全角符号（`，`、`。`、`、`、`「」`、`（）`）
  - 数字使用半角
  ```patch
  + 哇！你注意到这篇文章的要点了么？
  - 哇!你注意到这篇文章的要点了么?
  
  + 应用程序上下文（Application Context）通常用于下列情况。
  - 应用程序上下文(Application Context)通常用于下列情况。
  
  + 代码共有 100 行
  - 代码共有１００行
  ```
  
# 引用

- 使用 `[^1]` 进行脚注标记
