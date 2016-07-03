## 需要用到的 API  
### 几个 Object:  
1. Customer
2. Charge  
3. Dispute  
4. Refund  
5. Balance  

### 几种方法:  
1. create (Dispute 没有)  
2. retrieve  
3. update  
4. list  

### 两种 Payment  
1. Card  
2. Alipay  

- - -  ​

## 两个模块  
### 支付  
根据 Customer 付款
* 曾经支付过 --> 使用之前支付创建的 Customer 进行支付行为  
* 曾经未支付 --> 先为用户建立 Customer，允许用户使用不同方式支付(Alipay / Card)  

建立 Customer 并付款  
* 建立 Customer -- 根据燎原信息建立并在 server 保存 Cusomer id  

使用 Card 付款  
* 接收但不存储用户 Card 的相关信息  
* 根据 Customer id 为 Customer 建立  
* 建立 Charge  

使用支付宝付款  
* 接受用户的邮箱信息
* 验证支付宝验证码  
* 建立 Charge  


### 退款  
主要用到 Refund  
* 用户申请 Refund  
* 检查用户是否存在 Charge  
* 创建 Refund  

Dispute 无法被 create，应该不是我们这边做的

