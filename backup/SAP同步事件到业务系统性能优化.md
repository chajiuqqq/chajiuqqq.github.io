**背景**
EAP平台由多个子系统组成，比如MWMS、WMS、MES、OMS、EAP。上游对接ERP，下游处理工厂生产、仓储、销售、采购业务。

需要从ERP平台同步单据、基础数据到EAP平台。单据同步采用ERP触发事件方式同步，基础数据采用轮询同步。

问题在于轮询的实现，要求如下：
1. 提高轮询频率，保证实时性
2. 减少全量同步，避免子系统频繁rescache reload 

假设采用5分钟同步全部数据，导致实时性不高，提高频率会导致子系统频繁重载缓存。

**解决方法**
1. 采用滑动时间窗口同步，第一次窗口1970-now，第二次窗口now-now+1。比如第一次同步1970到2024-01-01的数据，全量更新，第二次就是2024-01-01到2024-01-01的数据
2. 当增量数据为空时，不通知子系统重载缓存，降低系统压力

**代码实现**
1. 增加时间窗口两个边界
![image](https://github.com/user-attachments/assets/e8eb017a-20ae-4115-9156-3136db50f498)

2. 收到事件时，左边界初始化为1970，右边界取当前时间
![image](https://github.com/user-attachments/assets/09391548-95e7-49d0-8083-c4286b98f3ad)

3. 一次同步完成后，更新左边界，并重置右边界。注意这里要进行值拷贝，不然左右边界都会变成nil
![image](https://github.com/user-attachments/assets/7862432d-e328-41fc-90e5-4d9a37173c6b)


事件处理流程：
![image](https://github.com/user-attachments/assets/70d087f9-1734-4f9e-8fe1-2cfb6f6d1c69)
