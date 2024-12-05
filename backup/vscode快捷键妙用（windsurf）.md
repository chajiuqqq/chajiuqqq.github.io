# 修改方法名后，如何自动修改引用的地方

方法一：使用 VS Code 的重命名功能
这是最直接且推荐的方法。
将光标放在要重命名的 Go 方法名上。
按下 `F2` 键 (或右键点击，选择 "Rename Symbol")。 这会打开一个重命名对话框。
输入新的方法名，然后按 Enter 键。 VS Code 会自动更新所有引用该方法名的地方。

# golang中，如何让vscode按照interface去生成未实现的方法？
方法二：使用快速修复功能
编译器会提示错误，指出未实现的方法。 将光标放在错误提示上。
VS Code 会提供一个快速修复选项，通常是 "Implement interface methods" 或类似的选项。 选择该选项即可生成未实现方法的代码框架。

# 按照文件名搜索
方法一：使用快速打开 (Ctrl+P 或 Cmd+P) 并精确匹配文件名:
这是最方便的方法，但需要你准确地知道文件名。 在快速打开面板中，输入完整的文件名（包括扩展名）。 如果文件名是唯一的，你就能直接找到它。 如果有多个同名文件，则需要进一步区分。

# 全局搜索
Windows 和 Linux: `Ctrl + Shift + F`
macOS: `Cmd + Shift + F`

# 立刻换行
`Ctrl + Enter`

# 复制当前行（不用选中）
- Windows 和 Linux: `Shift + Alt + ↓ `(向下复制一行) 或` Shift + Alt + ↑` (向上复制一行) 
- macOS: `Shift + Option + ↓` (向下复制一行) 或 `Shift + Option + ↑` (向上复制一行)