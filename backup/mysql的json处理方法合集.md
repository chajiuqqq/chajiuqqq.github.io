## 更新json中的某个属性

写一个mysql的sql，property列存储了json，格式是
```json
{"common": {"code": "HU001"}, "factory_code": "2010", "factory_name": "匈牙利恩捷", "work_shop_type": 1}
```
要求把factory_name从"匈牙利恩捷"更新为"SEMCORP Hungary Kft"

SQL:
```sql
UPDATE category_0
SET property = JSON_SET(property, '$.factory_name', 'SEMCORP Hungary Kft')
WHERE JSON_UNQUOTE(JSON_EXTRACT(property, '$.factory_name')) = '匈牙利恩捷';
```
解释：
- JSON_EXTRACT(property, '$.factory_name')：从JSON对象中提取factory_name的值。
- JSON_UNQUOTE()：去掉提取的JSON值中的引号，使其成为字符串。
- JSON_SET(property, '$.factory_name', 'SEMCORP Hungary Kft')：将factory_name的值更新为SEMCORP Hungary Kft。

## 查询json的某个字段 
```sql
SELECT JSON_UNQUOTE(JSON_EXTRACT(property, '$.location_identifier')) from category_0;
``` 

解释：
- JSON_EXTRACT(property, '$.factory_name')：从JSON对象中提取factory_name的值。
- JSON_UNQUOTE()：去掉提取的JSON值中的引号，使其成为字符串。

## 更新数组
假设某列存储了以下json：
```json
[
  {
    "Code": "",
    "Cargos": [
      {
        "Code": "39td2vv96xt9inh",
        "Type": 1,
        "Detail": {
          "BatchNum": "240722001",
          "Quantity": 1000000
        }
      }
    ]
  },
  {
    "Code": "",
    "Cargos": [
      {
        "Code": "a1b2c3d4e5",
        "Type": 2,
        "Detail": {
          "BatchNum": "240722002",
          "Quantity": 2000000
        }
      }
    ]
  }
]
```
- 按照索引更新数组里的对象：
```sql
UPDATE your_table
SET json_column = JSON_SET(
    json_column,
    '$[0].Cargos[0].Detail.BatchNum', '新的批号'
)
WHERE <condition>;
```
- 更新数组的每个对象的元素：
```sql
UPDATE your_table
SET json_column = JSON_SET(
    json_column,
    '$[*].Cargos[*].Detail.BatchNum', '新的批号'
)
WHERE <condition>;
```