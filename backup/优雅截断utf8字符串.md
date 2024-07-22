
## 背景
SAP接口某CHAR字段要求长度不超过25字节，该字段可能输入中文，如何优雅截断字符串？
- 如果转为byte截取，会导致结尾乱码可能
- 无法按照字符个数截取，因为可能中英文数字混合输入

## 解决方法
利用for range字符串时，每次获取一个rune（中英文或者数字），此时index是byte的index。这样就可以规范截取合法的index，避免乱码。


```go
func truncComments(in string) string {
	// 按照长度25优雅截断utf8字符串
	result := ""
	limit := 25
	right := 0
	for i := range in {
		if i <= limit {
			right = i
		} else {
			break
		}
	}
	result = in[:right]
	return result
}
```

测试：
```go
func TestTruncComments(t *testing.T) {
	s := "123fff哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈哈"
	o := truncComments(s)
	fmt.Println(o)
	fmt.Println(len([]byte(o)))
}

=== RUN   TestTruncComments
123fff哈哈哈哈哈哈
24
--- PASS: TestTruncComments (0.00s)
PASS
test end
```


## reference
> https://stackoverflow.com/a/46416000
> Slicing strings treats them as their underlying byte array; the slice operator operates on indexes of bytes, not of runes (which can be multiple bytes each). However, range over a string iterates on runes - but the index returned is of bytes. This makes it fairly straightforward to do what you're looking for ([full playground example here](https://play.golang.org/p/rfkfvbLWTM))