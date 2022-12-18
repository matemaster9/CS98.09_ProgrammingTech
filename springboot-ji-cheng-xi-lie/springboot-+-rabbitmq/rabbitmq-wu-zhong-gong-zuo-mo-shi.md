# rabbit-mq五种工作模式

## hello world

```java
@SneakyThrows
public static void main(String[] args) {
    // todo：创建mq连接
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo：设置mq连接工厂参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    String message = "RabbitMq: HelloWorld";
    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        channel.queueDeclare("HELLO_WORLD", false, false, false, null);
        channel.basicPublish("", "HELLO_WORLD", null, message.getBytes(StandardCharsets.UTF_8));
    }
}
```

```java
@SneakyThrows
public static void main(String[] args) {
    // todo：创建mq连接
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo：设置mq连接工厂参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        DeliverCallback deliverCallback = (consumerTag, delivery) -> log.info(new String(delivery.getBody(), StandardCharsets.UTF_8));
        channel.basicConsume("HELLO_WORLD", true, deliverCallback, consumerTag -> {
        });
    }
}
```

## work queues

```java
@SneakyThrows
public static void main(String[] args) {
    // todo：创建mq连接
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo：设置mq连接工厂参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    String message = "RabbitMq: WorkQueues";
    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        channel.queueDeclare("WORK_QUEUES", false, false, false, null);
        channel.basicPublish("", "WORK_QUEUES", null, (message + 1).getBytes(StandardCharsets.UTF_8));
        channel.basicPublish("", "WORK_QUEUES", null, (message + 2).getBytes(StandardCharsets.UTF_8));
        channel.basicPublish("", "WORK_QUEUES", null, (message + 3).getBytes(StandardCharsets.UTF_8));
    }
}
```

```java
@SneakyThrows
public static void main(String[] args) {
    // todo：创建mq连接
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo：设置mq连接工厂参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        DefaultConsumer consumer = new DefaultConsumer(channel) {
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("WorkQueuesConsume1Impl: " + new String(body, StandardCharsets.UTF_8));
            }
        };
        channel.basicConsume("WORK_QUEUES", true, consumer);
    }
}
```

## fanout

```java
@SneakyThrows
public static void main(String[] args) {
    // todo：创建mq连接
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo：设置mq连接工厂参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    String message = "RabbitMq: PubSub";
    String fanoutExchange = "pub_sub_fanout";
    String queue1 = "pub_sub_fanout_q1";
    String queue2 = "pub_sub_fanout_q2";
    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        // todo: 创建交换机
        channel.exchangeDeclare(fanoutExchange, BuiltinExchangeType.FANOUT, true, false, false, null);
        // todo：声明队列
        channel.queueDeclare(queue1, true, false, false, null);
        channel.queueDeclare(queue2, true, false, false, null);
        // todo：绑定队列交换机
        channel.queueBind(queue1, fanoutExchange, "");
        channel.queueBind(queue2, fanoutExchange, "");

        // todo：发消息
        channel.basicPublish(fanoutExchange, "", null, message.getBytes(StandardCharsets.UTF_8));
    }
}
```

```java
@SneakyThrows
public static void main(String[] args) {
    ConnectionFactory connectionFactory = new ConnectionFactory();

    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);
    connectionFactory.setHost("10.37.129.102");

    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        DeliverCallback deliverCallback = (consumerTag, delivery) -> log.info(new String(delivery.getBody(), StandardCharsets.UTF_8));
        channel.basicConsume("pub_sub_fanout_q1", true, deliverCallback, consumerTag -> {
        });
    }
}
```

## routing

```java
@SneakyThrows
public static void main(String[] args) {
    // todo: 创建连接工厂
    ConnectionFactory connectionFactory = new ConnectionFactory();

    // todo: 补充rabbit服务器参数
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    // todo：交换机与队列名称
    String message = "RabbitMq: Routing";
    String directExchange = "log_routing";
    String errorLogQueue = "error_queue";
    String infoLogQueue = "info_queue";
    String warnLogQueue = "warn_queue";
    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        // todo：声明交换机
        channel.exchangeDeclare(directExchange, BuiltinExchangeType.DIRECT, false);

        // todo：声明队列
        channel.queueDeclare(errorLogQueue, false, false, false, null);
        channel.queueDeclare(infoLogQueue, false, false, false, null);
        channel.queueDeclare(warnLogQueue, false, false, false, null);

        // todo：设置交换机与队列的绑定关系
        channel.queueBind(errorLogQueue, directExchange, "error");
        channel.queueBind(infoLogQueue, directExchange, "info");
        channel.queueBind(warnLogQueue, directExchange, "warn");

        // todo：利用指定routing-key想交换机发送消息
        channel.basicPublish(directExchange, "error", null, message.getBytes(StandardCharsets.UTF_8));
        channel.basicPublish(directExchange, "info", null, message.getBytes(StandardCharsets.UTF_8));
        channel.basicPublish(directExchange, "warn", null, message.getBytes(StandardCharsets.UTF_8));
    }
}
```

```java
@SneakyThrows
public static void main(String[] args) {
    ConnectionFactory connectionFactory = new ConnectionFactory();

    connectionFactory.setPort(5672);
    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");

    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        DeliverCallback deliverCallback = (consumerTag, delivery) -> {
            String message = new String(delivery.getBody(), "UTF-8");
            System.out.println(" [x] Received '" + delivery.getEnvelope().getRoutingKey() + "':'" + message + "'");
        };

        channel.basicConsume("error_queue", true, deliverCallback, consumerTag -> {
        });
    }
}
```

## topic

```java
@SneakyThrows
public static void main(String[] args) {
    ConnectionFactory connectionFactory = new ConnectionFactory();

    connectionFactory.setHost("10.37.129.102");
    connectionFactory.setUsername("mcc");
    connectionFactory.setPassword("matemaster98");
    connectionFactory.setPort(5672);

    String message = "RabbitMq: Topic";
    String topicExchange = "log_topic";
    try (Connection connection = connectionFactory.newConnection(); Channel channel = connection.createChannel()) {
        channel.exchangeDeclare(topicExchange, BuiltinExchangeType.TOPIC, false);
        channel.basicPublish(topicExchange, "error.#", null, message.getBytes(StandardCharsets.UTF_8));
    }
}
```
