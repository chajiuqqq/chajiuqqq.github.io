2种方法：

```go
num := int64(127)

// 1
res := strconv.FormatInt(num, 2)
fmt.Println(res)

// 2
res = fmt.Sprintf("%b", num)
fmt.Println(res)

// 3
res = fmt.Sprintf("%08b", num)
fmt.Println(res)

```

```
1111111
1111111
01111111
```

From: https://stackoverflow.com/questions/13870845/converting-from-an-integer-to-its-binary-representation