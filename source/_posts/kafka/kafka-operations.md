---
title: Kafka相关操作
date: 2023-05-05 15:21:00
categories:
- Kafka
---

# Kafka 相关操作

## 集群操作

### 创建主题

```sh
bin/kafka-topics.sh 
--create 
--zookeeper localhost:2181 
--replication-factor 3 
--partitions 1 
--topic topic-name
```

## Go 中使用

### 生产者

```go
import "github.com/Shopify/sarama"

func foo() {
    config := sarama.NewConfig()
    config.Version = sarama.V2_4_0_0

    config.Net.SASL.Enable = true
    config.Net.SASL.Handshake = true
    config.Net.SASL.Version = sarama.SASLHandshakeV1
    config.Net.SASL.User = "kafkaUser"
    config.Net.SASL.Password = "kafkaPass"
    config.Net.SASL.Mechanism = sarama.SASLTypeSCRAMSHA512
    config.Net.SASL.SCRAMClientGeneratorFunc = func() sarama.SCRAMClient { return &XDGSCRAMClient{HashGeneratorFcn: SHA512} }

    addrs := []string{"kafkaAddr"}

    producer, err := sarama.NewAsyncProducer(addrs, config)

    msg := &sarama.ProducerMessage{
        Topic: sarama.StringEncoder("topic"),
        Value: sarama.StringEncoder("value"),
    }

    // 将消息写入kafka
    select {
    case producer.Input() <- msg:
    default:
        break
    }
}
```

### 消费者组

```go
func foo() {
	config := sarama.NewConfig()
	config.Version = sarama.V2_4_0_0

	config.Net.SASL.Enable = true
	config.Net.SASL.Handshake = true
	config.Net.SASL.Version = sarama.SASLHandshakeV1
	config.Net.SASL.User = "kafkaUser"
	config.Net.SASL.Password = "kafkaPass"
	config.Net.SASL.Mechanism = sarama.SASLTypeSCRAMSHA512
	config.Net.SASL.SCRAMClientGeneratorFunc = func() sarama.SCRAMClient { return &XDGSCRAMClient{HashGeneratorFcn: SHA512} }

	addrs := []string{"kafkaAddr"}
	topics := []string{"topic"}

	group, err := sarama.NewConsumerGroup(addrs, "groupId", config)
	if err != nil {
		return err
	}
	defer func() { _ = group.Close() }()

	ctx := context.Background()

    // 无限循环，防止 server-side re-balance 发生，导致会话丢失
	for {
		handler := consumeHandler{}

		err := group.Consume(ctx, topics, handler)
		if err != nil {
			continue
		}
	}
}

type consumeHandler struct{}

func (consumeHandler) Setup(_ sarama.ConsumerGroupSession) error   { return nil }
func (consumeHandler) Cleanup(_ sarama.ConsumerGroupSession) error { return nil }
func (consumeHandler) ConsumeClaim(sess sarama.ConsumerGroupSession, claim sarama.ConsumerGroupClaim) error {
	for msg := range claim.Messages() {
		// 具体的消费逻辑
		sess.MarkMessage(msg, "")
	}
	return nil
}
```

### SCRAM 认证客户端

```go
var SHA512 scram.HashGeneratorFcn = func() hash.Hash { return sha512.New() }

// XDGSCRAMClient
type XDGSCRAMClient struct {
	*scram.Client
	*scram.ClientConversation
	scram.HashGeneratorFcn
}

// Begin 
func (x *XDGSCRAMClient) Begin(userName, password, authzID string) (err error) {
	x.Client, err = x.HashGeneratorFcn.NewClient(userName, password, authzID)
	if err != nil {
		return err
	}
	x.ClientConversation = x.Client.NewConversation()
	return nil
}

// Step
func (x *XDGSCRAMClient) Step(challenge string) (response string, err error) {
	response, err = x.ClientConversation.Step(challenge)
	return
}

// Done
func (x *XDGSCRAMClient) Done() bool {
	return x.ClientConversation.Done()
}
```
