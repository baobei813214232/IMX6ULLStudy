# 7.IIC
## 1. IIC通讯协议原理
1.  I2C 总线是一种串行数据总线，只有二根信号线，一根是双向的数据线SDA，另一根是时钟线SCL，两条线可以挂多个设备。 IIC设备（绝大多数）里有个固化的地址，只有在两条线上传输的值等于IIC设备的固化地址时，其才会作出响应。通常我们为了方便把IIC设备分为主设备和从设备，基本上谁控制时钟线（即控制SCL的电平高低变换）谁就是主设备。
2. IIC地址格式：8bit，LSB为读写控制位，LSB=0(WRITE), LSB=1(READ)
3. IIC相关信号：
   1. 初始化：SDA=1,SCL=1
   2. 起始控制位：SCL=1，SDA下降沿
   3. 结束控制位：SCL=2，SDA上升沿
   4. 起始信号/终止信号均由主机发送
4. 有效数据：SCL=1时，SDA的数据必须保持稳定，只有当SCL=0的时候，SDA的数据才可以发生边话
5. 当主机传输万一个字节数据之后，需要等待接收端的应答信号，接收端会令SDA=0，从而可以被主机接收到应答信号
6. 总线寻址
   1. 写：先写从设备地址，再写要写的寄存器地址，最后才写数据--所以只有第一次写才是地址数据
   2. 读：先写从设备地址，再写读的寄存器地址，还要再写一次从设备地址，最后才开始接收数据--第1步和第3步才是地址数据
   3. 一次完整的读写，是以一个结束STOP信号结尾，要再次接收到起始信号的时候，才会重新确定读写操作
7. 总线仲裁问题
   1. 当总线出现多个主机时，就会出现总线竞争问题。对于硬件IIC，可以通过对相关寄存器的读取，来判断总线是否出现裁决故障。

## 2. IMX6ULL硬件IIC操作流程
1. 设置时钟：IFDR[5:0],确定分频值，从而可以直接确定IIC频率
2. 控制寄存器
   1. bit7:IIC使能位
   2. bit5:主从模式选择
   3. bit4:发送接收设置
   4. bit2:产生重新开始信号
3. 状态寄存器：
   1. bit7:传输完成状态
   2. bit5：IIC状态繁忙状态
   3. bit0:读确认位，就是ACK信号
4. 数据寄存器：I2DR