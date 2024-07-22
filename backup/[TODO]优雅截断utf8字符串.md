> https://stackoverflow.com/a/46416000
> Slicing strings treats them as their underlying byte array; the slice operator operates on indexes of bytes, not of runes (which can be multiple bytes each). However, range over a string iterates on runes - but the index returned is of bytes. This makes it fairly straightforward to do what you're looking for ([full playground example here](https://play.golang.org/p/rfkfvbLWTM))


```
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