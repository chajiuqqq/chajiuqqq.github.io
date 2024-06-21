# prompt

帮我把go的struct定义转为proto message定义
1. 其中message的变量名称用小写+下划线格式命名
2. go中类似Enum[oms.FeedingType] 的类型转为proto的oms.FeedingType类型
3. 请把tag中的comment作为注释加到每行定义之后，并忽略其他tag。
4. time.Time类型使用string替换
5. 只输出message部分，不包含proto版本等其他信息

如果你明白了我的要求，请开始任务

以下是输入：
```
type FeedingReqHeader struct {
	OrderID            string                `mapstructure:"AUFNR,omitempty" comment:" 订单编号"`   // 订单编号
	PostingDate        time.Time             `mapstructure:"BUDAT,omitempty" comment:" 过账日期"`   // 过账日期
	DocumentDate       time.Time             `mapstructure:"BLDAT,omitempty" comment:" 凭证日期"`   // 凭证日期
	MovementType       Enum[oms.FeedingType] `mapstructure:"BWART,omitempty" comment:" 移动类型"`   // 移动类型
	DocumentHeaderText string                `mapstructure:"BKTXT,omitempty" comment:" 凭证抬头文本"` // 凭证抬头文本
	Reference          string                `mapstructure:"XBLNR,omitempty" comment:" 参照"`     // 参照
}
type FeedingReqLine struct {
	MaterialCode          string                 `mapstructure:"MATNR,omitempty" comment:" 物料编码"`     // 物料编码
	BatchNumber           string                 `mapstructure:"CHARG,omitempty" comment:" 批次号"`      // 批次号
	Quantity              string                 `mapstructure:"ERFMG,omitempty" comment:" 数量"`       // 数量
	Unit                  string                 `mapstructure:"ERFME,omitempty" comment:" 单位"`       // 单位
	Location              string                 `mapstructure:"LGORT,omitempty" comment:" 存储地点"`     // 存储地点
	ReservationID         string                 `mapstructure:"RSNUM,omitempty" comment:" 预留号"`      // 预留号
	ReservationItem       string                 `mapstructure:"RSPOS,omitempty" comment:" 预留号行项目"`   // 预留号行项目
	SpecialStockIndicator Enum[oms.SpecialStock] `mapstructure:"SOBKZ,omitempty" comment:" 特殊库存标识"`   // 特殊库存标识
	SalesOrderID          string                 `mapstructure:"KDAUF,omitempty" comment:" 销售订单号"`    // 销售订单号
	SalesOrderItem        string                 `mapstructure:"KDPOS,omitempty" comment:" 销售订单行号"`   // 销售订单行号
	ResStatus             Enum[oms.ResStatus]    `mapstructure:"ZSTATUS,omitempty" comment:" 处理结果状态"` // 处理结果状态
	ResMessage            string                 `mapstructure:"ZMSG,omitempty" comment:" 处理结果信息"`    // 处理结果信息
	ReturnDocNo           string                 `mapstructure:"MBLNR,omitempty" comment:" 物料凭证号"`    // 物料凭证号
	ReturnDocLine         string                 `mapstructure:"ZEILE,omitempty" comment:" 物料凭证行号"`   // 物料凭证行号
	ReturnDocYear         string                 `mapstructure:"ZYEAR,omitempty" comment:" 凭证年度"`     // 凭证年度
}

```

# response

以下是将Go的struct定义转换为Proto Message定义的结果：

```protobuf
message FeedingReqHeader {
  string order_id = 1; // 订单编号
  string posting_date = 2; // 过账日期
  string document_date = 3; // 凭证日期
  oms.FeedingType movement_type = 4; // 移动类型
  string document_header_text = 5; // 凭证抬头文本
  string reference = 6; // 参照
}

message FeedingReqLine {
  string material_code = 1; // 物料编码
  string batch_number = 2; // 批次号
  string quantity = 3; // 数量
  string unit = 4; // 单位
  string location = 5; // 存储地点
  string reservation_id = 6; // 预留号
  string reservation_item = 7; // 预留号行项目
  oms.SpecialStock special_stock_indicator = 8; // 特殊库存标识
  string sales_order_id = 9; // 销售订单号
  string sales_order_item = 10; // 销售订单行号
  oms.ResStatus res_status = 11; // 处理结果状态
  string res_message = 12; // 处理结果信息
  string return_doc_no = 13; // 物料凭证号
  string return_doc_line = 14; // 物料凭证行号
  string return_doc_year = 15; // 凭证年度
}
```

注意事项：
1. 变量名称按照要求使用小写加下划线的格式命名。
2. 类似`Enum[oms.FeedingType]`的类型转换为`oms.FeedingType`类型。
3. `mapstructure`的comment标签已作为注释添加到每行定义之后，其他标签已忽略。
4. `time.Time`类型已替换为`string`类型。
5. 结果只包含message部分，不包含其他信息（如Proto版本等）。
