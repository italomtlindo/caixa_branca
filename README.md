
# Teste de Caixa Branca — Análise de Erros

## 1. Contextualização sobre Teste de Caixa Branca

O teste de caixa branca é uma técnica de teste de software que analisa a estrutura interna do código-fonte. Ele permite verificar os comandos, as condições, as decisões e os diferentes caminhos que podem ser percorridos durante a execução de um programa.

O objetivo desse tipo de teste é identificar erros na lógica do código e verificar se cada condição funciona conforme o esperado. Para isso, são analisadas as entradas, o processamento, os resultados e os caminhos de execução.

Nesta atividade, foi utilizado um código JavaScript responsável por calcular o valor de um pedido de compra. O sistema possui três produtos: notebook, mouse e teclado, cada um com seu preço e sua quantidade disponível em estoque.

O programa permite selecionar um produto, informar a quantidade desejada, utilizar cupons de desconto e escolher o tipo de frete. Depois, calcula o subtotal, os descontos, o frete e o valor final do pedido.

Durante a atividade, foram analisados sete erros relacionados às condições do código. Para cada erro, foram identificados o comportamento apresentado, o resultado esperado, a correção necessária e o novo teste.

### Código utilizado na atividade

```javascript
const precos = {
  notebook: 3000,
  mouse: 80,
  teclado: 150
};

const estoque = {
  notebook: 5,
  mouse: 20,
  teclado: 10
};

const produto = document.getElementById("produto");
const quantidade = document.getElementById("quantidade");
const cupom = document.getElementById("cupom");
const frete = document.getElementById("frete");
const calcular = document.getElementById("calcular");
const resultado = document.getElementById("resultado");

function calcularDesconto(subtotal, codigo) {
  if (codigo === "SENAI10") {
    return subtotal * 0.10;
  }

  if (codigo === "SENAI20" && subtotal >= 1000) {
    return subtotal * 0.20;
  }

  return 0;
}

function calcularFrete(tipo, subtotal) {
  if (tipo === "retirada") {
    return 0;
  }

  if (tipo === "expresso") {
    return 60;
  }

  if (subtotal >= 500) {
    return 0;
  }

  return 30;
}

function finalizarPedido() {
  const produtoSelecionado = produto.value;
  const qtd = Number(quantidade.value);
  const codigo = cupom.value.trim().toUpperCase();

  if (qtd <= 0) {
    resultado.innerHTML = "<p>Quantidade inválida.</p>";
    return;
  }

  if (qtd > estoque[produtoSelecionado]) {
    resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
    return;
  }

  const subtotal = precos[produtoSelecionado] * qtd;
  const desconto = calcularDesconto(subtotal, codigo);
  const valorFrete = calcularFrete(frete.value, subtotal);

  let total = subtotal - desconto + valorFrete;

  if (qtd > 5) {
    total = total - subtotal * 0.05;
  }

  if (total >= 3000) {
    total = total * 0.95;
  }

  let mensagem = "Pedido calculado com sucesso.";

  if (total <= 0) {
    mensagem = "Valor do pedido inválido.";
  } else if (total >= 3000) {
    mensagem = "Pedido de alto valor.";
  }

  resultado.innerHTML = `
    <p>${mensagem}</p>
    <p>Subtotal: R$ ${subtotal.toFixed(2)}</p>
    <p>Desconto: R$ ${desconto.toFixed(2)}</p>
    <p>Frete: R$ ${valorFrete.toFixed(2)}</p>
    <p class="total">Total: R$ ${total.toFixed(2)}</p>
  `;
}

calcular.addEventListener("click", finalizarPedido);
```

O código apresentado acima corresponde à versão com as correções dos sete erros.

---

## 2. Análise das Estruturas de Decisão

O código utiliza estruturas condicionais para determinar quais caminhos serão executados. Os principais comandos encontrados são `if`, `else if` e `return`.

### 2.1. Erro 1 — Cupom SENAI20

A função `calcularDesconto()` verifica se o cupom informado é válido e se o subtotal atende ao valor mínimo exigido.

```javascript
if (codigo === "SENAI10") {
  return subtotal * 0.10;
}

if (codigo === "SENAI20" && subtotal >= 1000) {
  return subtotal * 0.20;
}

return 0;
```

O cupom SENAI10 aplica 10% de desconto. Já o cupom SENAI20 aplica 20% quando o subtotal é maior ou igual a R$ 1.000. Se nenhuma condição for satisfeita, o desconto será zero.

O erro estava na condição do cupom SENAI20, que anteriormente utilizava `subtotal >= 500`. A correção foi alterar o limite para `subtotal >= 1000`.

### 2.2. Erro 2 — Frete grátis

A função `calcularFrete()` verifica o tipo de frete selecionado e o valor do subtotal.

```javascript
if (tipo === "retirada") {
  return 0;
}

if (tipo === "expresso") {
  return 60;
}

if (subtotal >= 500) {
  return 0;
}

return 30;
```

Quando o cliente escolhe retirada, o frete é gratuito. No frete expresso, o valor é R$ 60. Para as demais modalidades, pedidos com subtotal maior ou igual a R$ 500 recebem frete grátis. Caso contrário, o frete custa R$ 30.

O erro estava no uso do operador `>`, que não incluía pedidos de exatamente R$ 500. A correção foi utilizar `>=`.

### 2.3. Erro 3 — Validação da quantidade

O programa verifica se a quantidade informada é válida.

```javascript
if (qtd <= 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

Anteriormente, a condição utilizava `qtd < 0`, permitindo que a quantidade zero continuasse no programa. A correção foi utilizar `qtd <= 0`, impedindo pedidos com quantidades iguais ou menores que zero.

### 2.4. Erro 4 — Verificação do estoque

O programa verifica se a quantidade solicitada ultrapassa o estoque disponível.

```javascript
if (qtd > estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}
```

A condição anterior utilizava `>=`, bloqueando compras cuja quantidade fosse exatamente igual ao estoque disponível. A correção foi utilizar `>`, permitindo comprar toda a quantidade disponível, sem ultrapassar o estoque.

### 2.5. Erro 5 — Desconto para mais de cinco unidades

O programa verifica se a quantidade comprada é superior a cinco unidades.

```javascript
if (qtd > 5) {
  total = total - subtotal * 0.05;
}
```

Anteriormente, o desconto era calculado multiplicando o total por `0.95`. A correção foi descontar 5% do subtotal, utilizando `total - subtotal * 0.05`.

### 2.6. Erro 6 — Desconto para pedidos de alto valor

O programa verifica se o total do pedido é maior ou igual a R$ 3.000.

```javascript
if (total >= 3000) {
  total = total * 0.95;
}
```

A condição anterior utilizava `total > 3000`, deixando de incluir pedidos de exatamente R$ 3.000. A correção foi utilizar `total >= 3000`.

### 2.7. Erro 7 — Mensagem final do pedido

O programa determina a mensagem apresentada ao cliente com base no total final.

```javascript
if (total <= 0) {
  mensagem = "Valor do pedido inválido.";
} else if (total >= 3000) {
  mensagem = "Pedido de alto valor.";
}
```

Se o total for menor ou igual a zero, o programa informa que o valor é inválido. Se o total for maior ou igual a R$ 3.000, informa que o pedido é de alto valor. Nos demais casos, mantém a mensagem de sucesso.

O erro estava na utilização do subtotal para verificar se o pedido era de alto valor. A correção foi utilizar o total final do pedido.

---

## 3. Fluxograma do Exemplo

Os fluxogramas foram organizados na ordem dos sete erros identificados. Cada um apresenta a entrada, as condições executadas, o caminho percorrido, o processamento, o resultado, a comparação com o comportamento esperado, a identificação do erro, a correção e o novo teste.

### 3.1. Fluxograma — Erro 1: Cupom SENAI20

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Cupom: SENAI20           │
│ Subtotal: R$ 1.000       │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÕES EXECUTADAS     │
│ Cupom == SENAI20?        │
│ Subtotal >= 1000?        │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ As condições são         │
│ verdadeiras              │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ 1000 × 20% = R$ 200      │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Desconto de R$ 200       │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ O desconto deve ser      │
│ aplicado a partir de     │
│ R$ 1.000                 │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ O limite anterior era    │
│ R$ 500                   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ subtotal >= 1000         │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Testar cupom SENAI20     │
│ com subtotal de R$ 1.000 │
└──────────────────────────┘
```

### 3.2. Fluxograma — Erro 2: Frete grátis

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Subtotal: R$ 500         │
│ Frete: normal            │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÃO EXECUTADA       │
│ subtotal >= 500?         │
└────────────┬─────────────┘
             ↓
       ┌───────────┐
       │ SIM       │
       └─────┬─────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ O pedido recebe          │
│ frete grátis             │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ Frete = R$ 0             │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Frete grátis a partir    │
│ de R$ 500                │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ Pedidos de R$ 500        │
│ também devem ter         │
│ frete grátis             │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ A condição usava > 500   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ subtotal >= 500          │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Testar subtotal de       │
│ exatamente R$ 500        │
└──────────────────────────┘
```

### 3.3. Fluxograma — Erro 3: Quantidade inválida

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Quantidade = 0           │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÃO EXECUTADA       │
│ qtd < 0?                 │
└────────────┬─────────────┘
             ↓
       ┌───────────┐
       │ NÃO       │
       └─────┬─────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ O programa continua      │
│ com quantidade zero      │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ O subtotal é calculado   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ O pedido continua        │
│ indevidamente            │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ Quantidade zero deve     │
│ ser inválida             │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ O zero não era           │
│ verificado               │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ qtd <= 0                 │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Informar quantidade 0    │
│ e verificar a mensagem   │
└──────────────────────────┘
```

### 3.4. Fluxograma — Erro 4: Estoque

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Quantidade solicitada    │
│ igual ao estoque         │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÃO EXECUTADA       │
│ qtd >= estoque?          │
└────────────┬─────────────┘
             ↓
       ┌───────────┐
       │ SIM       │
       └─────┬─────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ O programa bloqueia      │
│ a compra                 │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ A execução é interrompida│
│ pelo return              │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Quantidade indisponível  │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ Deve permitir comprar    │
│ toda a quantidade        │
│ disponível               │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ O operador >= bloqueava  │
│ a quantidade exata       │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ qtd > estoque            │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Comprar exatamente a     │
│ quantidade do estoque    │
└──────────────────────────┘
```

### 3.5. Fluxograma — Erro 5: Desconto para mais de cinco unidades

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Quantidade maior que 5   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÃO EXECUTADA       │
│ qtd > 5?                 │
└────────────┬─────────────┘
             ↓
       ┌───────────┐
       │ SIM       │
       └─────┬─────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ Aplica desconto          │
│ adicional                │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ total = total -          │
│ subtotal × 0.05          │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Desconto de 5% sobre     │
│ o subtotal               │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ O desconto deve ser      │
│ calculado sobre o        │
│ subtotal                 │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ O cálculo anterior       │
│ aplicava 5% sobre        │
│ o total inteiro          │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ total = total -          │
│ subtotal * 0.05          │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Testar pedido com        │
│ mais de 5 unidades       │
└──────────────────────────┘
```

### 3.6. Fluxograma — Erro 6: Desconto para pedidos de alto valor

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Total = R$ 3.000         │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÃO ANTERIOR        │
│ total > 3000?            │
└────────────┬─────────────┘
             ↓
       ┌───────────┐
       │ NÃO       │
       └─────┬─────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ O desconto não é         │
│ aplicado                 │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ Total permanece          │
│ em R$ 3.000              │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Não recebe o desconto    │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ R$ 3.000 também deve     │
│ entrar na condição       │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ O operador > excluía     │
│ o valor exato            │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ total >= 3000            │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Testar um pedido de      │
│ exatamente R$ 3.000      │
└──────────────────────────┘
```

### 3.7. Fluxograma — Erro 7: Mensagem de pedido de alto valor

```text
┌──────────────────────────┐
│         ENTRADA          │
│ Dados do pedido          │
│ Subtotal e total final   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CONDIÇÕES EXECUTADAS     │
│ total <= 0?              │
│ total >= 3000?           │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CAMINHO PERCORRIDO       │
│ Define a mensagem        │
│ conforme o total final   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ PROCESSAMENTO            │
│ Verifica o valor final   │
│ do pedido                │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ RESULTADO                │
│ Pedido de alto valor     │
│ quando total >= 3000     │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ COMPARAÇÃO               │
│ A mensagem deve          │
│ considerar o total final │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ IDENTIFICAÇÃO DO ERRO    │
│ A condição anterior      │
│ utilizava o subtotal     │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ CORREÇÃO                 │
│ Utilizar total >= 3000   │
└────────────┬─────────────┘
             ↓
┌──────────────────────────┐
│ NOVO TESTE               │
│ Testar pedido cujo       │
│ subtotal e total sejam   │
│ diferentes               │
└──────────────────────────┘
```

---

## 4. Casos de Teste

Com base na análise do código e dos caminhos identificados, foram definidos sete casos de teste para verificar as condições corrigidas.

| Identificação | Entrada | Condição/Caminho | Resultado Esperado |
|---|---|---|---|
| CT01 | Cupom SENAI20 e subtotal de R$ 1.000 | Cupom válido e subtotal >= 1000 | Aplicar 20% de desconto |
| CT02 | Subtotal de R$ 500 e frete normal | Subtotal >= 500 | Frete igual a R$ 0 |
| CT03 | Quantidade igual a 0 | qtd <= 0 | Exibir "Quantidade inválida" |
| CT04 | Quantidade igual ao estoque | qtd > estoque | Permitir a compra |
| CT05 | Quantidade maior que 5 | qtd > 5 | Aplicar desconto de 5% sobre o subtotal |
| CT06 | Total igual a R$ 3.000 antes do desconto por valor elevado | total >= 3000 | Aplicar desconto adicional de 5% |
| CT07 | Total final maior ou igual a R$ 3.000 | total >= 3000 na verificação da mensagem | Exibir "Pedido de alto valor" |

Os testes verificam principalmente os limites das condições, como valores iguais a zero, ao estoque disponível, a R$ 500, a R$ 1.000 e a R$ 3.000.

---

## 5. Resultados dos Testes

A tabela abaixo apresenta os resultados esperados para os casos de teste após a aplicação das correções. Para que os resultados sejam considerados evidências de execução, os testes devem ser executados no programa e os resultados reais devem ser registrados.

| Teste | Entrada | Resultado Esperado | Resultado Obtido | Situação |
|---|---|---|---|---|
| CT01 | Cupom SENAI20 e subtotal de R$ 1.000 | Desconto de R$ 200 | A preencher após execução | Pendente de confirmação |
| CT02 | Subtotal de R$ 500 e frete normal | Frete de R$ 0 | A preencher após execução | Pendente de confirmação |
| CT03 | Quantidade 0 | Mensagem "Quantidade inválida" | A preencher após execução | Pendente de confirmação |
| CT04 | Quantidade igual ao estoque | Compra permitida | A preencher após execução | Pendente de confirmação |
| CT05 | Quantidade maior que 5 | Desconto adicional de 5% sobre o subtotal | A preencher após execução | Pendente de confirmação |
| CT06 | Total de R$ 3.000 antes do desconto por valor elevado | Aplicação do desconto adicional de 5% | A preencher após execução | Pendente de confirmação |
| CT07 | Total final maior ou igual a R$ 3.000 | Mensagem "Pedido de alto valor" | A preencher após execução | Pendente de confirmação |

### Evidências dos testes

Após executar o programa, podem ser adicionadas capturas de tela do navegador mostrando as entradas e os resultados obtidos.

As imagens podem ser salvas em uma pasta chamada `imagens` e referenciadas neste documento.

Exemplos de referências para as capturas:

- `imagens/teste01.png`
- `imagens/teste02.png`
- `imagens/teste03.png`
- `imagens/teste04.png`
- `imagens/teste05.png`
- `imagens/teste06.png`
- `imagens/teste07.png`

As imagens devem ser adicionadas somente depois de serem capturadas durante a execução real do programa.

---

## 6. Análise dos Resultados

A análise das estruturas condicionais permitiu identificar sete problemas relacionados aos limites das condições, à validação de dados e ao cálculo de descontos.

No primeiro erro, o limite do cupom SENAI20 foi ajustado para que o desconto de 20% seja aplicado quando o subtotal for maior ou igual a R$ 1.000.

No segundo erro, a condição do frete foi alterada para incluir pedidos de exatamente R$ 500, utilizando o operador `>=`.

No terceiro erro, a validação da quantidade foi corrigida para impedir valores iguais ou menores que zero.

No quarto erro, a verificação do estoque foi ajustada para permitir compras com quantidade exatamente igual ao estoque disponível, bloqueando apenas quantidades superiores.

No quinto erro, o cálculo do desconto para compras com mais de cinco unidades foi modificado para descontar 5% do subtotal, em vez de multiplicar o total inteiro por 0,95.

No sexto erro, a condição de desconto para pedidos de alto valor foi alterada para incluir valores exatamente iguais a R$ 3.000.

No sétimo erro, a verificação da mensagem final foi ajustada para utilizar o total do pedido, considerando os descontos e o frete, em vez de utilizar apenas o subtotal.

Os fluxogramas demonstram os caminhos que devem ser percorridos em cada situação. Os casos de teste foram definidos para verificar se as condições corrigidas produzem os resultados esperados.

A confirmação dos resultados obtidos depende da execução dos testes no programa. Após essa etapa, será possível registrar quais testes foram aprovados e incluir as capturas de tela correspondentes.

---

## 7. Conclusão

A realização desta atividade permitiu compreender a importância do teste de caixa branca no desenvolvimento de software. Por meio da análise da estrutura interna do código, foi possível identificar condições que poderiam produzir resultados diferentes dos esperados.

A análise dos sete erros demonstrou que pequenas diferenças nos operadores de comparação, como `>` e `>=`, podem alterar o comportamento de um programa. Também foi possível observar a importância de validar corretamente os dados de entrada, respeitar os limites do estoque e calcular os descontos de acordo com as regras definidas.

Os fluxogramas facilitaram a visualização dos caminhos de execução, desde a entrada dos dados até a identificação do erro, sua correção e a definição de um novo teste.

Os casos de teste foram elaborados para verificar os principais caminhos identificados durante a análise. A execução desses testes e o registro dos resultados reais são etapas necessárias para confirmar que todas as correções funcionam como esperado.

Conclui-se que o teste de caixa branca é importante para melhorar a confiabilidade do software, pois permite analisar as decisões internas do programa, identificar falhas na lógica e verificar se as condições implementadas correspondem ao comportamento esperado.
