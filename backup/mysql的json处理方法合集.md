
## 更新json中的某个属性

写一个mysql的sql，property列存储了json，格式是
```
{"common": {"code": "HU001"}, "factory_code": "2010", "factory_name": "匈牙利恩捷", "work_shop_type": 1}
```
要求把factory_name从"匈牙利恩捷"更新为"SEMCORP Hungary Kft"

SQL:
```
UPDATE category_0
SET property = JSON_SET(property, '$.factory_name', 'SEMCORP Hungary Kft')
WHERE JSON_UNQUOTE(JSON_EXTRACT(property, '$.factory_name')) = '匈牙利恩捷';
```
解释：
- JSON_EXTRACT(property, '$.factory_name')：从JSON对象中提取factory_name的值。
- JSON_UNQUOTE()：去掉提取的JSON值中的引号，使其成为字符串。
- JSON_SET(property, '$.factory_name', 'SEMCORP Hungary Kft')：将factory_name的值更新为SEMCORP Hungary Kft。

## 查询json的某个字段 
```
SELECT JSON_UNQUOTE(JSON_EXTRACT(property, '$.location_identifier')) from category_0;
``` 

解释：
- JSON_EXTRACT(property, '$.factory_name')：从JSON对象中提取factory_name的值。
- JSON_UNQUOTE()：去掉提取的JSON值中的引号，使其成为字符串。