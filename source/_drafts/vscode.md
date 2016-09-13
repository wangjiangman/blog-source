


```
// 将设置放入此文件中以覆盖默认设置
{
    // 按 "Tab" 时插入空格。该设置在 `editor.detectIndentation` 启用时根据文件内容进行重写。
    "editor.insertSpaces": false,

    // 当打开文件时，将基于文件内容检测 "editor.tabSize" 和 "editor.insertSpaces"。
    "editor.detectIndentation": false,

	// 配置 glob 模式以排除文件和文件夹。
	"files.exclude": {
		"**/.git": true,
		"**/.svn": true,
		"**/.hg": true,
		"**/.DS_Store": true,
		"node_modules/": true,
		"typings/": true,
		"app/**/*.js": true,
		"app/**/*.js.map": true
	}
}
```

