

# 项目地址

[ATM项目：https://github.com/SkyOceanchen/ATM](https://github.com/SkyOceanchen/ATM)

# 项目需求如下:


- 额度15000或自定义  ---> 注册
- 实现购物商城，买东西加 购物车，调用信用卡接口结账 ---> 购物车功能,支付功能
- 可以提现，手续费5% ---> 提现
- 支持多账户登录 ---> 登录
- 支持账户间转账 ---> 转账
- 记录日常消费流水 ---> 记录流水
- 提供还款接口  ---> 还款
- ATM记录操作日志 ---> 记录日志
- 提供管理接口，包括添加账户、用户额度，冻结账户等...  ---> 管理员功能
- 用户认证功能 ---> 登录认证装饰器


# 用户界面功能
    1.注册
    2.登录
    3.查看额度
    
    4.提现
    5.还款
    6.转账
    
    7.查看流水
    
    8.购物功能
    9.查看购物车
    
    10.注销
    q.退出

一个项目是如何从无到有:
    1.需求分析:

    2.程序的架构设计
        用户视图层:
            用户与程序交互的.
            小的逻辑判断
    
        接口层:
            业务逻辑的处理
    
        数据层:
            对数据进行存\取
    
    不设计程序架构的问题:
        1.逻辑不清晰
        2.结构不清晰
        3.不便于维护
    
    设计程序的好处:
        1.逻辑清晰
        2.结构清晰
        3.便于维护
        4.程序的解耦合
    
    3.分任务开发
        项目经理:
            把开发任务分发给开发人员:
                提高项目开发效率
                较少项目开发周期
    
    4.测试:
        黑盒测试:
            对用户能看到的操作,进行测试.
    
        白盒测试:
            对程序的性能进行测试.
    
    5.上线运行
        交给运维人员部署上线,运营.