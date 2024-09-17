# AWS
WorkFlow Delivery






Explicação:
EscolhaLanche: Estado de escolha para selecionar o tipo de lanche.
EscolhaBebida: Estado de escolha para selecionar a bebida.
AceitePedido: Tarefa que envia a confirmação do pedido ao cliente.
ModoPagamento: Estado de escolha para selecionar o método de pagamento.
ManufaturaPedido: Tarefa que notifica o cliente sobre o tempo estimado de preparo.
AvisoEntrega: Tarefa que avisa o cliente quando o pedido sai para entrega.
EntregaFeita: Tarefa que envia notificações de conclusão da transação e agradecimento.
Você pode substituir REGION e ACCOUNT_ID pelos valores apropriados da sua conta AWS. Este fluxo pode ser importado diretamente na AWS Step Functions para criar o workflow desejado.






{
  "Comment": "Fluxo de Delivery de Fastfood",
  "StartAt": "EscolhaLanche",
  "States": {
    "EscolhaLanche": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.lanche",
          "StringEquals": "Burguer Simples",
          "Next": "EscolhaBebida"
        },
        {
          "Variable": "$.lanche",
          "StringEquals": "Burguer Sofisticado",
          "Next": "EscolhaBebida"
        },
        {
          "Variable": "$.lanche",
          "StringEquals": "Burguer Ultra Sofisticado",
          "Next": "EscolhaBebida"
        }
      ],
      "Default": "EscolhaBebida"
    },
    "EscolhaBebida": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.bebida",
          "StringEquals": "Suco Natural",
          "Next": "AceitePedido"
        },
        {
          "Variable": "$.bebida",
          "StringEquals": "Refrigerante",
          "Next": "AceitePedido"
        },
        {
          "Variable": "$.bebida",
          "StringEquals": "Cerveja",
          "Next": "AceitePedido"
        }
      ],
      "Default": "AceitePedido"
    },
    "AceitePedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:EnviarConfirmacao",
      "Next": "ModoPagamento"
    },
    "ModoPagamento": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.pagamento",
          "StringEquals": "PIX",
          "Next": "ManufaturaPedido"
        },
        {
          "Variable": "$.pagamento",
          "StringEquals": "Cartão Débito",
          "Next": "ManufaturaPedido"
        },
        {
          "Variable": "$.pagamento",
          "StringEquals": "Cartão Credito",
          "Next": "ManufaturaPedido"
        }
      ],
      "Default": "ManufaturaPedido"
    },
    "ManufaturaPedido": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:NotificarCliente",
      "Next": "AvisoEntrega"
    },
    "AvisoEntrega": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:AvisarEntrega",
      "Next": "EntregaFeita"
    },
    "EntregaFeita": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT_ID:function:EnviarNotificacaoConclusao",
      "End": true
    }
  }
}

