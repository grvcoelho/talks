![fit](https://user-images.githubusercontent.com/7416751/27385181-14dc2baa-5668-11e7-8a87-0f994ecaa61b.jpg)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## oi :)

### :man: Guilherme Coelho
### :office: Pagar.me
### :house_with_garden: Jundiaí

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## ![inline 100%](https://user-images.githubusercontent.com/7416751/27462430-37c616aa-5797-11e7-8f20-39f02a691cca.jpg)

## Pagar.me Checkout

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Overview do Produto :pager:

### O que é o Pagar.me Checkout?

---

![](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Overview do Produto

1. Checkout transparente
1. Captura os dados do pagador
1. Experiência do Usuário
1. Checkout customizável
1. Autoriza transação no banco

---

![fit](https://user-images.githubusercontent.com/7416751/27385981-bcb647be-566a-11e7-91e4-9163ad4105bf.jpg)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

![inline](https://user-images.githubusercontent.com/7416751/27385981-bcb647be-566a-11e7-91e4-9163ad4105bf.jpg)

## [bit.ly/ostc2a](https://bit.ly/ostc2a)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Como utilizar? :nut_and_bolt:

### Via formulário, via checkout api

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Utilizando via formulário

```html
<form method="POST" action="/comprar">
  <script type="text/javascript"
    src="https://assets.pagar.me/checkout/checkout.js"
    data-button-text="Pagar um café"
    data-encryption-key="ek_test_*************************xqRmk"
    data-amount="2025">
  </script>
</form>
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Utilizando via API

```javascript
const button = document.querySelector('#opensource-day')
button.addEventListener('click', openCheckout)

function openCheckout () {
  const checkout = new PagarMeCheckout.Checkout({
    encryption_key: "ek_test_*************************xqRmk",
    success: () => alert('Transação autorizada! :)')
  })

  checkout.open({
    amount: 2025,
    installments: 2,
    payment_method: 'credit_card'
  })
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Use Cases :card_index:

### Modo token, modo card_hash

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo Token

### `data-create-token="true"`

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo Token

1. Cria transação de cartão e autoriza no banco
1. Retorna um `token` da transação
1. Não tem captura automática
1. Autorização dura **5 horas**

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo Token

```javascript
{
  amount: 14350,
  payment_method: 'credit_card',
  createToken: true // default
}
```

Retorno:

```yml
{
  "token": "test_transaction_CS2Eixp1aVYNfzCnVtLzkd1iUiZ8b3",
  "payment_method": "credit_card"
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo CardHash

### `data-create-token="false"`

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo CardHash

1. Não cria transação
1. Retorna o `card_hash` (que representa os dados de cartão)
1. `card_hash` pode ser usado **apenas 1 vez**
1. `card_hash` tem duração de **5 minutos**
1. Use case: assinaturas

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Modo CardHash

```javascript
{
  amount: 14350,
  payment_method: 'credit_card',
  createToken: false
}
```

Retorno:

```yml
{
  "payment_method": "credit_card",
  "amount": 14350,
  "installments": "4",
  "card_hash": "123456_laNbu********VqnNWpMA"
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Detalhes Técnicos :computer:

### Como o checkout foi construído?

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Endpoints da API

### Quais endpoints da API usamos no checkout?

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Chamada de endpoint da API

```
GET https://api.pagar.me/1/endpoint
```

```yml
{
  encryption_key: ek_test_******************2xa4b
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Preencher endereço do cliente

```
GET /zipcodes/04551902
```

```yml
{
  "zipcode": "04551902",
  "street": "Rua Fidêncio Ramos 308",
  "neighborhood": "Vila Olímpia",
  "city": "São Paulo",
  "state": "SP"
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Calcular pagamentos parcelados

```
GET /transactions/calculate_installments
```

```yml
{
  "installments": {
    "1": {
      "installment": 1,
      "amount": 10000,
      "installment_amount": 10000
    },
    "2": {
      "installment": 2,
      "amount": 10598,
      "installment_amount": 5299
    }
  }
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Criar card_hash

```
GET /transactions/card_hash_key
```

```yml
{
  "date_created": "2015-02-27T15:44:26.000Z",
  "id": 111111,
  "ip": "000.0.00.00",
  "public_key": "-----BEGIN PUBLIC KEY-----\ -----END PUBLIC KEY-----\ "
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Criar transação

```
POST /transactions
```

```yml
{
  "status": "authorized",
  "amount": 1000,
  "payment_method": "credit_card",
  "capture_method": "ecommerce",
  "referer": "encryption_key",
  ...
}
```

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

# pagarme-js (lib)

![inline](https://user-images.githubusercontent.com/7416751/27460995-f746b6d8-578d-11e7-94d3-98c66f7e4b24.png)

### [https://github.com/pagarme/pagarme-js](https://github.com/pagarme/pagarme-js)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Tecnologia :microscope:

### O que usamos para construir o checkout

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Tecnologia

1. Modo "clássico" de desenvolvimento web
1. Javascript + jQuery
1. CSS + Sass
1. Gulp
1. Tests: Docker + phantomJS

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Arquitetura :wrench:

### Como o checkout funciona internamente

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Arquitetura

1. Arquivos: checkout.js e modal.js
1. Steps de pagamento (customer\_data, credit\_card)
1. Execução dentro de iframe (via CDN)
1. Não tem versionamento (pros e cons)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Comunicação Iframe

### Comunicação host - iframe - pagarme

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Comunicação Iframe

1. Comunicação via tag `<title>`
1. Suporte IE6+
1. easyXDM: lib de comunicação entre iframe e host
1. Client não precisa de HTTPS (embora seja recomendado)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Iframe - fluxo de comunicação

![inline fluxo-de-comunicacao](https://user-images.githubusercontent.com/7416751/27389873-152e3ca2-5676-11e7-8be8-c493ab80fb37.png)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Roadmap

### Onde estamos, para onde vamos

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Onde estamos

![inline](https://user-images.githubusercontent.com/7416751/27391673-ea38a316-567a-11e7-9470-137d178cabb9.png)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Onde estamos

1. Funciona bem para as features que já estão implementadas
1. Código legado + Tecnologia Antiga
1. Dificuldade de debuggar problemas
1. Dificuldade de implementar novas features

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Para onde vamos? :telescope:

### O futuro do Checkout

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Reconstruir o Checkout

### Tecnologia, design e experiência modernas

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

# :earth_americas: OPEN SOURCE :sparkling_heart:

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Vamos fazer juntos? :muscle:

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Links

### Github, Documentação, etc

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

# Github

![inline](https://user-images.githubusercontent.com/7416751/27387024-eada78ba-566d-11e7-95d6-74a69550a965.png)

### [https://github.com/pagarme/pagarme-checkout](https://github.com/pagarme/pagarme-checkout)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

# Documentação

![inline](https://user-images.githubusercontent.com/7416751/27387330-d321cbf0-566e-11e7-9b2b-1b00f0ea56fc.png)

### [https://docs-beta.pagar.me/docs/overview-checkout](https://docs-beta.pagar.me/docs/overview-checkout)

---

![fundo](https://user-images.githubusercontent.com/7416751/27387680-c12bc7c4-566f-11e7-9f56-30585b39078f.png)

## Obrigado :sparkling_heart:

![inline](https://user-images.githubusercontent.com/7416751/27461941-8ac364b0-5793-11e7-8a1f-bdfb5c6f1126.png)

### [https://github.com/grvcoelho](https://github.com/grvcoelho)
