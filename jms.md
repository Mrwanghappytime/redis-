# jms



## 1.jms成员

- provider
- product
- consum
- message

### 1.1 product

```java
package com.haofeng.mq;

import org.apache.activemq.ActiveMQConnectionFactory;
import org.apache.activemq.ActiveMQSslConnectionFactory;

import javax.jms.*;


public class Product {
    public static void main(String[] args) throws Exception {
        String url = "tcp://192.168.171.132:61616";
        String name = "01";
        //创建工厂
        ActiveMQConnectionFactory activeMQConnectionFactory = new ActiveMQConnectionFactory(url);
        //通过工厂，获取connection
        Connection connection = activeMQConnectionFactory.createConnection();
        connection.start();
        //创建session ,两个参数,第一个为事务，第二个为签收
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);
        //创建目的地（队列或者主题）
        Queue destination = session.createQueue(name);
        //创建消息的生产者
        MessageProducer producer = session.createProducer(destination);
        //通过Producer发送到消息队列中
        for(int i=0;i<3;i++){
            //创建消息
           TextMessage textMessage =  session.createTextMessage("发送message" + i);//理解为字符串
            //通过messageProdycer发送到消息队列
            producer.send(textMessage);
        }
        producer.close();
        session.close();
        connection.close();
    }
}

```

### 1.2 consum

```java
package com.haofeng.mq;

import org.apache.activemq.ActiveMQConnectionFactory;

import javax.jms.*;

public class Consumm {
    public static void main(String[] args) throws Exception{
        String url = "tcp://192.168.171.132:61616";
        String name = "01";
        //创建工厂
        ActiveMQConnectionFactory activeMQConnectionFactory = new ActiveMQConnectionFactory(url);
        //通过工厂，获取connection
        Connection connection = activeMQConnectionFactory.createConnection();
        connection.start();
        //创建session ,两个参数,第一个为事务，第二个为签收
        Session session = connection.createSession(false,Session.AUTO_ACKNOWLEDGE);
        Queue destination = session.createQueue(name);
        MessageConsumer consumer = session.createConsumer(destination);
        while(true) {
            TextMessage message = (TextMessage) consumer.receiveNoWait();
            if(message == null){
                break;
            }
            System.out.println(message.getText());
        }
        consumer.close();
        session.close();
        connection.close();

    }
}

```

### 1.3 message

#### 1.3.1消息头

- JMSDestination    目的地
- JMSDeliveryMode  是否持久化
- JMSExpiration       消息过期时间
- JMSPriority            优先级，加急一定比普通快，默认为4 大于4为加急
- JMSMessageId     消息ID，由消息中间件产生，可以用于防止重复消费

#### 1.3.2消息体

##### 1.3.2.1消息体格式

- textmessage   普通字符串类型包含一个string
- MapMessage   一个Map类型的消息，key为String 值为java的基本类型
- BytesMessage  二进制数组，包含一个byte数组
- StreamMessage  java数据流信息，用标准流操作来顺序的填充和读取
- ObjectMessage   对象消息，包含一个序列化的java对象

注：发送和接受消息类型一直

#### 1.3.3 消息属性

可用于消息判断，增加相关内容，进行区别对待

set基本类型Property

注：只要消息被接受了，就算消息处理完成，不管处理结果如何

#### 1.3.4 消息的持久化

#### 1.3.5 java内嵌

```java
package com.haofeng.mq;

import org.apache.activemq.broker.Broker;
import org.apache.activemq.broker.BrokerService;

public class Emane {
    public static void main(String[] args) throws Exception {
        BrokerService brokerService = new BrokerService();
        brokerService.setUseJmx(true);
        brokerService.addConnector("tcp://localhost:61616");
        brokerService.start();
    }
}

```







###### 1

./activemq start xbean:file:/配置文件路径

























