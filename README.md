# Consumindo e Produzindo Mensagens na Fila SQS

Este projeto demonstra como consumir e produzir mensagens usando o Amazon SQS (Simple Queue Service) com a ajuda do LocalStack.

## Pré-requisitos

- Java 21
- Maven
- Docker
- LocalStack 
- AWS CLI

## Instruções de Configuração

### 1. Iniciar o LocalStack

Para iniciar o LocalStack, execute o seguinte comando no terminal:

```bash
localstack start
```

### 2. Configurar Credenciais AWS
Em seguida, configure suas credenciais AWS com o comando:
```bash
aws configure
```
Use as seguintes configurações para o projeto:
```bash
AWS Access Key ID [None]: test
AWS Secret Access Key [None]: test
Default region name [None]: us-east-1
Default output format [None]: json
```

### 3. Criar uma Fila SQS
Você pode criar uma fila SQS usando o AWS CLI ou pela interface web do LocalStack.

Pela linha de comando:
```bash
aws --endpoint-url=http://localhost:4566 sqs create-queue --queue-name minha-fila
```
Usando a Interface Web do LocalStack:
- Abra seu navegador e acesse a interface web do LocalStack em https://app.localstack.cloud/dashboard.
- No menu, vá até "Default Instance".
- Dentro do painel, entre na seção "SQS"
- Clique em Create Queue.
- Insira o nome da fila, por exemplo, minha-fila, selecione a região "us-east-1" e clique em Create.

## Implementação em Java
### Produtor de Mensagens (SqsProducer)
1. Criação do Cliente SQS:
```bash
SqsClient sqsClient = SqsClient.builder()
        .region(Region.US_EAST_1)
        .endpointOverride(URI.create("http://localhost:4566"))
        .build();
```
Inicializa o cliente SQS com a região e o endpoint do LocalStack.

2. Envio de Mensagem:
```bash
SendMessageRequest sendMsgRequest = SendMessageRequest.builder()
        .queueUrl(QUEUE_URL)
        .messageBody(message)
        .delaySeconds(0)
        .build();

sqsClient.sendMessage(sendMsgRequest);
```
- Cria um pedido de envio de mensagem e o envia para a fila especificada.

### Consumidor de Mensagens (SqsConsumer)
1. Criação do Cliente SQS:
```bash
SqsClient sqsClient = SqsClient.builder()
        .region(Region.US_EAST_1)
        .endpointOverride(URI.create("http://localhost:4566"))
        .build();
```
- Semelhante ao produtor, inicializa o cliente SQS.
2. Recebendo mensagens:
```bash
ReceiveMessageRequest receiveRequest = ReceiveMessageRequest.builder()
        .queueUrl(QUEUE_URL)
        .maxNumberOfMessages(5)
        .waitTimeSeconds(10)
        .build();
```
- Configura o pedido para receber até 5 mensagens com um tempo de espera de 10 segundos.
3. Processamento e Exclusão
```bash
for (Message message : messages) {
    System.out.println("Mensagem recebida: " + message.body());
    DeleteMessageRequest deleteRequest = DeleteMessageRequest.builder()
            .queueUrl(QUEUE_URL)
            .receiptHandle(message.receiptHandle())
            .build();
    sqsClient.deleteMessage(deleteRequest);
}
```
Processa cada mensagem recebida e a exclui da fila após o processamento.

### Saídas do Terminal
Produtor de Mensagens (SqsProducer)

![Envio de mensagens pelo SqsProducer](https://github.com/devGabrielPedro/fila-sqs/blob/e0615d9900e2e97b73af5a5f5b3e192c90abaec2/src/images/sqs-producer.png)
- Descrição: Isso indica que a mensagem foi enviada com sucesso para a fila SQS. O texto da mensagem enviada será exibido.
  
Consumidor de Mensagens (SqsConsumer)

![Recebimento de mensagens pelo SqsConsumer](https://github.com/devGabrielPedro/fila-sqs/blob/e0615d9900e2e97b73af5a5f5b3e192c90abaec2/src/images/sqs-consumer.png)
- Descrição: Isso indica que o consumidor recebeu a mensagem da fila SQS. O corpo da mensagem recebida será exibido.
