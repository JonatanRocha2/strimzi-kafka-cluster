# Projeto de Instalação do Kafka com Strimzi Operator

Este projeto descreve o processo de instalação e configuração do Apache Kafka usando o Strimzi Operator em um cluster Kubernetes.

## Índice

1. [Pré-requisitos](#pré-requisitos)
2. [Instalação do Strimzi Operator](#instalação-do-strimzi-operator)
3. [Criação de um Cluster Kafka](#criação-de-um-cluster-kafka)
4. [Configuração Adicional](#configuração-adicional)
5. [Testando a Instalação](#testando-a-instalação)
6. [Referências](#referências)

## Pré-requisitos

Antes de iniciar, certifique-se de ter os seguintes itens:

- Um cluster Kubernetes em funcionamento (você pode usar Minikube, GKE, EKS, AKS, etc.)
- `kubectl` instalado e configurado para acessar seu cluster Kubernetes
- Helm instalado (opcional, mas recomendado para facilidade de gerenciamento)

## Instalação do Strimzi Operator

1. Adicione o repositório Helm do Strimzi:

    ```bash
    helm repo add strimzi https://strimzi.io/charts/
    helm repo update
    ```

2. Instale o Strimzi Operator no namespace `kafka` (ou qualquer namespace de sua escolha):

    ```bash
    kubectl create namespace kafka
    helm install strimzi-kafka strimzi/strimzi-kafka-operator --namespace kafka

    ou

    kubectl apply -f 1-strimzi-cluster/values.yaml    
    ```

3. Verifique se o Strimzi Operator está em execução:

    ```bash
    kubectl get pods -n kafka
    ```

## Criação de um Cluster Kafka

1. Crie um arquivo YAML para o cluster Kafka (por exemplo, `kafka-cluster.yaml`):

    ```bash
    kubectl apply -f 2-server/1-kafka-metrics.yaml
    kubectl apply -f 2-server/2-kafka-server.yaml
    ```

2. Aplique o arquivo YAML para criar o cluster Kafka:

    ```bash
    kubectl apply -f kafka-cluster.yaml
    ```

3. Verifique se os pods Kafka e Zookeeper estão em execução:

    ```bash
    kubectl get pods -n kafka
    ```

4. Adicionalmente, instale os componentes canary, bridge, connect, connector, schema-registry, ui e as métricas.

    ```bash
    kubectl apply -f 3-canary/
    kubectl apply -f 4-bridge/
    kubectl apply -f 5-connect/
    kubectl apply -f 6-schema-registry/
    kubectl apply -f 7-ui/
    kubectl apply -f 8-metrics/
    kubectl apply -f 9-metrics/

    **Opcional**
    kubectl apply -f 10-topics/
    ```

## Configuração Adicional

Para configurações adicionais, como persistência de dados, configuração de segurança e ajustes de performance, consulte a [documentação oficial do Strimzi](https://strimzi.io/docs/operators/latest/).

## Testando a Instalação

1. Crie um tópico Kafka para teste:

    ```bash
    kubectl run kafka-producer -ti --image=strimzi/kafka:latest-kafka-3.0.0 --rm=true --restart=Never -- bin/kafka-topics.sh --create --topic my-topic --bootstrap-server my-cluster-kafka-bootstrap:9092 --partitions 1 --replication-factor 1
    ```

2. Produza e consuma mensagens para verificar a funcionalidade:

    Produzir mensagens:

    ```bash
    kubectl run kafka-producer -ti --image=strimzi/kafka:latest-kafka-3.0.0 --rm=true --restart=Never -- bin/kafka-console-producer.sh --topic my-topic --bootstrap-server my-cluster-kafka-bootstrap:9092
    ```

    Consumir mensagens:

    ```bash
    kubectl run kafka-consumer -ti --image=strimzi/kafka:latest-kafka-3.0.0 --rm=true --restart=Never -- bin/kafka-console-consumer.sh --topic my-topic --bootstrap-server my-cluster-kafka-bootstrap:9092 --from-beginning
    ```

## Referências

- [Strimzi Documentation](https://strimzi.io/docs/operators/latest/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)