# Kafka

以下示例均在 Kafka 4.1.1 测试通过。

## 在 Docker 安装 Kafka

```bash shell
docker pull apache/kafka:4.1.1
docker run --name kafka \
--restart unless-stopped \
-e "KAFKA_PROCESS_ROLES=broker,controller" \
-e "KAFKA_NODE_ID=1" \
-e "KAFKA_CONTROLLER_QUORUM_VOTERS=1@localhost:9093" \
-e "KAFKA_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093" \
-e "KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL" \
-e "KAFKA_CONTROLLER_LISTENER_NAMES=CONTROLLER" \
-e "KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://<主机地址>:9092" \
-p 9092:9092 \
-d apache/kafka:4.1.1

sudo mkdir -p /home/kafka-ui/conf
sudo touch /home/kafka-ui/conf/config.yaml
sudo chmod 777 /home/kafka-ui/conf/config.yaml
docker pull provectuslabs/kafka-ui:v0.7.2
docker run --name kafka-ui \
--restart unless-stopped \
-e "DYNAMIC_CONFIG_ENABLED=true" \
-v /home/kafka-ui/conf/config.yaml:/etc/kafkaui/dynamic_config.yaml \
-p 8080:8080 \
-d provectuslabs/kafka-ui:v0.7.2
```
