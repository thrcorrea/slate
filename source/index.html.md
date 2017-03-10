# API Recarga Agora

#1. Introdução
Com a API Recarga Agora é possível gerar recargas para cartões de diversas o operadoras de diversos estados do brasil.

## 1.1. Comunicação

### 1.1.1. JSON sobre HTTPS
A única forma de comunicação suportada é JSON sobre HTTPS. Os dados devem esta codificados no formato UTF-8. Os headers HTTP devem indicar "Content-Type" como "application/json". O header "Accept" deve ser usado e deve indicar "application/json".

### 1.1.2. Respostas de Sucesso
Respostas de sucesso apresentam status HTTP 200, com o corpo da mensagem contendo um objeto JSON no formato especificado para o serviço.

> Exemplo de mensagem de erro:

```json
{
  "code": 13,
  "message": "Não foi possível comunicar com o serviço de Recarga"
}
```

### 1.1.3. Respostas de Erro
Respostas que indicam erro podem ser identificadas pelo código de status HTTP 400 (client error) ou 500 (server error). O corpo da resposta de erro sempre contém um código de erro e uma mensagem descrevendo o motivo do erro ter ocorrido. [Tabela de Códigos de erro.](#anexo-a-c-digos-de-erro)

#2. Chamadas da API

##2.1 Issuers
**Caminho**: `GET <endpoint>/issuers?appId=`

**Descrição**: Retorna os issuers de acordo com o appId provido

>Exemplo de requisição:

```text
url = <endpoint>/issuers?appId=1
```

**REQUISIÇÃO**:

***Path***

|Campo |Descrição |Formato |Obrigatório |
|------|----------|:------:|:----------:|
|**appId**| Id do aplicativo utilizado como referência | Number | Sim |

> Exemplo de resposta:

```json
[
  {
    "issuer_id": 1,
    "thumb": "shj8",
    "name": "TEU",
    "nr_digits": 15,
    "checkBalance": true,
    "mask": "XXXXXXXXXXXXXX-X"
  },
  [...]
]
```

**RESPOSTA**

|Campo |Descrição |Formato
|------|----------|:-----:|
|**issuer_id**| Id do issuer | Integer |
|**thumb**| Thumbnail | String |
|**name**| Nome do issuer | String |
|**nr_digits**| Número de digitos do cartão | Number |
|**checkBalance**| Valor informando se é possível checar o saldo do cartão | Boolean |
|**mask**| Mascara do cartão | String |

##2.2 Products
**Caminho**: `GET <endpoint>/issuers/{id}/products`

**Descrição**: Retorna os produtos do Issuer selecionado.

> Exemplo de requisição:

```text
url = <endpoint>/issuers/1/products
```

**REQUISIÇÃO**:

***Path***

|Campo |Descrição |Formato |Obrigatório |
|------|----------|:------:|:----------:|
|**id**| Id do Issuer | Integer | Sim |

> Exemplo de resposta:

```json
[
  {
    "id": 2,
    "name": "Bilhete Personalizado Plus",
    "thumb": "http://i.imgur.com/id23RN0.png",
    "color": "#dfc701",
    "rechargeable": true,
    "externalId": "2"
  },
  [...]
]
```

**RESPOSTA**

|Campo |Descrição |Formato
|------|----------|:-----:|
|**id**| Id do produto | Integer |
|**name**| Nome do produto | String |
|**thumb**| Endereço de imagem do produto | String |
|**color**| Cor do produto | String |
|**rechargeable**| Valor informando se o produto é recarregável | Boolean |
|**externalId**| Id externo do produto | Integer |

##2.3 Product Values
**Caminho**: `GET <endpoint>/issuers/{id}/products/{idProduct}/values`

**Descrição**: Retorna os valores disponíveis para recarga do Produto selecionado.

> Exemplo de requisição:

```text
url = <endpoint>/issuers/1/products/1/values
```

**REQUISIÇÃO**

***Path***

|Campo |Descrição |Formato |Obrigatório |
|------|----------|:------:|:----------:|
|**id**| Id do Issuer | Integer | Sim |
|**idProduct**| Id do Produto | Integer | Sim |

> Exemplo de resposta

```json
[
  {
    "id": 9,
    "value": 20,
    "productId": 7,
    "feeValue": 150
  },
  [...]
]
```
**RESPOSTA**

|Campo |Descrição |Formato
|------|----------|:-----:|
|**id**| Id do valor | Integer |
|**value**| Valor de recarga em reais | Number |
|**productId**| Id do produto | Integer |
|**feeValue**| Valor da taxa em centavos Ex: "150" para R$1,50. | Number |

##2.4 Valid Card
**Caminho**: `GET <endpoint>/validateCard?issuerNumber&issuerId`

**Descrição**: Retorna se o cartão provido é válido ou não.

> Exemplo de requisição:

```text
url = <endpoint>/validateCard?issuerNumber=000000000000000&issuerId=
```

**REQUISIÇÃO**:

***Path***

|Campo |Descrição |Formato |Obrigatório |
|------|----------|:------:|:----------:|
|**issuerNumber**|Número do cartão a ser validado| Number | Sim |
|**issuerId**| Id da operadora do cartão | Number | Sim |

> Exemplo de resposta:

```json
{
  "valid" : true
}
```

**RESPOSTA**:

|Campo |Descrição |Formato|
|------|----------|:-----:|
|**valid**| Valor informando se o cartão provido é válido ou não | Boolean |

##2.5 Third Party Recharge
**Caminho**: `POST <endpoint>/thirdPartyRecharge`

**Descrição**: Gera uma recarga de acordo com as informações providas na requisição.

> Exemplo de requisição:

```json
{
	"sessionToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVC...",
	"issuerId": "1",
	"productId": "1",
	"issuerNr": "000000000000000",
	"amount": 2000
}
```

**REQUISIÇÃO**

***Body***

|Campo |Descrição |Formato |Obrigatório
|------|----------|:------:|:----------:|
|**sessionToken**| Token de autenticação | String | Sim
|**issuerId**| Id da operadora do cartão | String | Sim
|**productId**| Id do cartão | String | Sim
|**issuerNr**| Número do cartão a ser recarregado | String | Sim
|**amount**| Valor em centavos a ser recarregado. Ex: "2000" para R$20,00. | Number | Sim

> Exemplo de resposta

```json
{
  "message": "Recarga efetuada com sucesso"
}
```

**RESPOSTA**:

|Campo |Descrição |Formato
|------|----------|:-----:|
|**message**| Mensagem informando se a recarga foi efetuada com sucesso | String


# Anexo A - Códigos de erro

|Código |Descrição |
|-------|----------|
|9| Token inválido |
|10| Token expirado |
|11| Ip inválido |
|12| Produto não encontrado |
|13| Amount inválido |
