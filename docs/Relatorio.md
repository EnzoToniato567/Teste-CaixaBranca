# Relatório de Erros — Sistema de Pedidos

- **Aluno:** Enzo Semenssi Toniato
- **Turma:** 3ºB EM
- **Professores:** Robson e Reenye
- **Data:** 30/09/2026

---

| Erro | Tipo de erro lógico |
|---|---|
| 1 | Limite mínimo errado |
| 2 | Operador de comparação errado |
| 3 | Falta de validação do valor informado |
| 4 | Regra de negócio não verificada |
| 5 | Limite de desconto errado |
| 6 | Limites diferentes para a mesma decisão |

## Erro 1

| Campo | Registro |
|---|---|
| **Nível** | Fácil |
| **Trecho do código** | `if (qtd < 0)` |
| **O que deveria acontecer** | A quantidade precisa ser maior que zero. |
| **Teste feito** | Mouse, quantidade `0` e frete normal. |
| **O que o código fez** | Como `0 < 0` é falso, o pedido continuou normalmente. |
| **Resultado esperado** | “Quantidade inválida.” |
| **Resultado encontrado** | O pedido foi calculado com total de R$ 30,00. |
| **Problema** | O sistema aceitou quantidade zero. |
| **Como foi corrigido** | Trocar por `qtd <= 0`. |
| **Depois da correção** | Agora aparece “Quantidade inválida.” |

**Fluxograma**

```text
Quantidade informada: 0
       |
       v
Quantidade é válida?
   /          \
 Sim          Não
  |             |
  v             v
Calcula      Exibe erro
pedido
```

## Erro 2

| Campo | Registro |
|---|---|
| **Nível** | Fácil |
| **Trecho do código** | `if (qtd >= estoque[produtoSelecionado])` |
| **O que deveria acontecer** | A compra da última unidade em estoque deve ser permitida. |
| **Teste feito** | Mouse, quantidade `20` e estoque `20`. |
| **O que o código fez** | Como `20 >= 20` é verdadeiro, ele bloqueou o pedido. |
| **Resultado esperado** | Pedido de R$ 1.600,00. |
| **Resultado encontrado** | “Quantidade indisponível em estoque.” |
| **Problema** | O sistema entendeu que pegar exatamente a quantidade disponível era falta de estoque. |
| **Como foi corrigido** | Trocar por `qtd > estoque[produtoSelecionado]`. |
| **Depois da correção** | O pedido é calculado normalmente. |

**Fluxograma**

```text
Quantidade: 20 | Estoque: 20
       |
       v
Quantidade excede o estoque?
   /                    \
 Sim                    Não
  |                       |
  v                       v
Exibe erro            Calcula pedido
```

## Erro 3

| Campo | Registro |
|---|---|
| **Nível** | Médio |
| **Trecho do código** | `const qtd = Number(quantidade.value)` |
| **O que deveria acontecer** | O sistema deve aceitar somente quantidades inteiras. |
| **Teste feito** | Mouse, quantidade `1.5` e frete normal. |
| **O que o código fez** | O valor passou pelas verificações e gerou subtotal de R$ 120,00. |
| **Resultado esperado** | “Quantidade inválida.” |
| **Resultado encontrado** | O pedido foi calculado com total de R$ 150,00. |
| **Problema** | Não havia uma verificação para saber se o número era inteiro. |
| **Como foi corrigido** | Usar `!Number.isInteger(qtd) \|\| qtd <= 0` |
| **Depois da correção** | Agora aparece “Quantidade inválida.” |

**Fluxograma**

```text
Quantidade informada: 1,5
       |
       v
Quantidade é inteira?
   /          \
 Sim          Não
  |             |
  v             v
Calcula      Exibe erro
pedido
```

## Erro 4

| Campo | Registro |
|---|---|
| **Nível** | Médio |
| **Trecho do código** | `if (codigo === "SENAI10")` |
| **O que deveria acontecer** | O cupom `SENAI10` só pode ser usado em pedidos a partir de R$ 1.000,00. |
| **Teste feito** | 1 mouse, cupom `SENAI10` e frete normal. |
| **O que o código fez** | Com subtotal de R$ 80,00, ele aplicou 10% de desconto direto. |
| **Resultado esperado** | Total de R$ 110,00, sem desconto. |
| **Resultado encontrado** | Total de R$ 102,00. |
| **Problema** | Faltava checar o valor mínimo do subtotal. |
| **Como foi corrigido** | Usar `codigo === "SENAI10" && subtotal >= 1000`. |
| **Depois da correção** | O total fica em R$ 110,00. |

**Fluxograma**

```text
Cupom: SENAI10 | Subtotal: R$ 80
       |
       v
Subtotal é maior ou igual a R$ 1.000?
   /                         \
 Sim                         Não
  |                            |
  v                            v
Aplica 10%                 Sem desconto
```

## Erro 5

| Campo | Registro |
|---|---|
| **Nível** | Difícil |
| **Trecho do código** | `if (qtd > 5)` |
| **O que deveria acontecer** | A partir de 5 itens, o pedido deve receber 5% de desconto. |
| **Teste feito** | 5 teclados, sem cupom e frete normal. |
| **O que o código fez** | Como `5 > 5` é falso, o desconto não foi aplicado. |
| **Resultado esperado** | Total de R$ 712,50. |
| **Resultado encontrado** | Total de R$ 750,00. |
| **Problema** | O pedido com exatamente 5 itens ficou de fora do desconto. |
| **Como foi corrigido** | Trocar por `qtd >= 5`. |
| **Depois da correção** | O total fica em R$ 712,50. |

**Fluxograma**

```text
Quantidade informada: 5
       |
       v
Quantidade é maior ou igual a 5?
   /                    \
 Sim                    Não
  |                       |
  v                       v
Aplica 5%             Mantém o total
de desconto
```

## Erro 6

| Campo | Registro |
|---|---|
| **Nível** | Difícil |
| **Trecho do código** | `if (total > 3000)` e `else if (total >= 3000)` |
| **O que deveria acontecer** | Um pedido de R$ 3.000,00 deve ganhar 5% de desconto de alto valor. |
| **Teste feito** | 1 notebook, sem cupom e frete normal. |
| **O que o código fez** | O total de R$ 3.000,00 não passou no `> 3000`, mas passou no `>= 3000`. |
| **Resultado esperado** | Total de R$ 2.850,00 e mensagem de pedido de alto valor. |
| **Resultado encontrado** | Total de R$ 3.000,00, mas com a mensagem de pedido de alto valor. |
| **Problema** | O desconto e a mensagem usavam limites diferentes. |
| **Como foi corrigido** | Usar `>= 3000` no desconto e salvar o total anterior para fazer a classificação. |
| **Depois da correção** | Total de R$ 2.850,00 e mensagem de pedido de alto valor. |

**Fluxograma**

```text
Total do pedido: R$ 3.000
       |
       v
Total é maior ou igual a R$ 3.000?
   /                         \
 Sim                         Não
  |                            |
  v                            v
Aplica 5%                 Pedido comum
de desconto
  |
  v
Exibe pedido de alto valor
```

---

## Observações extras (não obrigatórias)

- Mostrar no `index.html` quantos produtos ainda estão disponíveis de cada item.
- Tirar o `placeholder="Ex.: SENAI10"`, para o cupom não ficar tão evidente.