# Análise Léxica — Guia de Estudo Completo

> Compiladores · Baseado no material da disciplina + padrão dos exercícios Ybadoo (Linguagem SIMPLE)

---

## 1. O que é Análise Léxica

- É a **primeira fase** do compilador.
- Quem faz esse trabalho é o **analisador léxico** (também chamado **scanner**).
- Função: ler o programa fonte **caractere a caractere** e traduzi-lo em uma sequência de **tokens** (símbolos léxicos).
- Descarta o que não é significativo: espaços em branco, comentários.
- A saída do léxico (sequência de tokens) alimenta a fase seguinte: o **analisador sintático**.

**Visão formal — cada fase enxerga o código de um jeito diferente:**

| Fase | Como enxerga o código-fonte |
|---|---|
| Léxico | Sequência de palavras de uma **linguagem regular** |
| Sintático | Sequência de tokens formando sentenças de uma **linguagem livre de contexto** |

💡 **Por que isso importa para prova:** questões de ENADE adoram perguntar "o analisador léxico garante que o programa está correto?" — a resposta é **não**. Ele só garante que os *símbolos* são válidos (regras léxicas), não que a *estrutura* do programa (sintaxe) está certa. Isso é trabalho do analisador sintático.

---

## 2. Tokens

Cada token carrega **3 informações**:

1. **Classe** → o tipo do token (identificador, operador, palavra reservada...)
2. **Valor** → depende da classe (ex: índice na tabela de símbolos)
3. **Posição** → (linha, coluna) onde apareceu — usada para relatar erros

### Dois grupos de tokens

| Tipo | Característica | Exemplo |
|---|---|---|
| **Token simples** | Não tem valor associado — a classe já descreve tudo | operador `+`, palavra reservada `if`, delimitador |
| **Token com argumento** | Tem valor associado, pois é definido pelo programador | identificador `x`, constante `13` |

**Exemplo genérico** (`while I < 100 do I := J + I;`):
```
[while, ][id, 7][<, ][cte, 13][do, ][id, 7][:=, ][id, 12][+, ][id, 7][;, ]
```

---

## 3. Tabela de Símbolos

- Estrutura de dados que armazena **nomes** definidos no programa (identificadores, e no caso da linguagem SIMPLE, também números de linha e constantes).
- Guarda atributos: tipo, escopo, etc.
- Começa a ser construída **já durante a análise léxica**.
- Toda vez que um identificador/constante aparece: consulta a tabela → se já existe, reaproveita o índice; se não existe, insere um novo código sequencial.

⚠️ **Erro comum:** achar que cada *ocorrência* de um símbolo ganha um código novo. Não! O código é por **valor distinto**. Se `x` aparece 5 vezes no programa, todas as 5 ocorrências apontam para o **mesmo** código na tabela.

---

## 4. Como o Analisador Léxico Funciona (algoritmo)

Baseia-se em um **autômato finito determinístico (AFD)**:

```
RECONHECE(M, T)
1  s ← ESTADO-INICIAL(M)
2  while TEM-SÍMBOLOS(T)
3    do c ← PRÓXIMO-SÍMBOLO(T)
4       if EXISTE-PRÓXIMO-ESTADO(M, s, c)
5          then s ← PRÓXIMO-ESTADO(M, s, c)
6          else return false
7  if ESTADO-FINAL(M, s)
8     then return true
9     else return false
```

**Em palavras:** parte do estado inicial, consome caractere por caractere avançando de estado; se não existe transição válida para aquele caractere, o token é rejeitado; se ao final o autômato está em um estado final, o token foi reconhecido.

---

## 5. Linguagem SIMPLE — Regras Gerais

- Cada instrução = **número de linha** + **comando**.
- Números de linha devem aparecer em **ordem crescente**.
- Apenas **letras minúsculas**.
- Nome de variável = **uma única letra**, sempre do tipo **inteiro**.

### Comandos

| Comando | Exemplo | O que faz |
|---|---|---|
| `rem` | `50 rem comentário` | Comentário — ignorado pelo compilador |
| `input` | `30 input x` | Lê inteiro do teclado e armazena em x |
| `let` | `80 let u = j - 36` | Atribuição |
| `print` | `10 print w` | Exibe o valor |
| `goto` | `70 goto 45` | Desvio incondicional para a linha 45 |
| `if` | `35 if i == x goto 80` | Desvio condicional — se verdadeiro, salta; senão, continua |
| `end` | `99 end` | Termina a execução do programa |

### Operadores

| Categoria | Símbolos |
|---|---|
| Atribuição | `=` |
| Aritméticos | `+` `-` `*` `/` `%` |
| Relacionais | `>` `>=` `<` `<=` `==` `!=` |

---

## 6. Tabela de Códigos de Tokens da Linguagem SIMPLE ⚠️ (DECORAR)

**Delimitadores**
| Símbolo | Código |
|---|---|
| Nova linha (LF/ENTER) | **10** |
| Fim de texto (ETX/ETF) | **03** |

**Atribuição**
| Símbolo | Código |
|---|---|
| `=` | **11** |

**Operadores aritméticos**
| Símbolo | Código |
|---|---|
| `+` | 21 |
| `-` | 22 |
| `*` | 23 |
| `/` | 24 |
| `%` | 25 |

**Operadores relacionais**
| Símbolo | Código |
|---|---|
| `==` | 31 |
| `!=` | 32 |
| `>` | 33 |
| `<` | 34 |
| `>=` | 35 |
| `<=` | 36 |

**Identificadores e constantes**
| Tipo | Código |
|---|---|
| Variável | **41** |
| Constante numérica inteira | **51** |

**Palavras reservadas**
| Palavra | Código |
|---|---|
| `rem` | 61 |
| `input` | 62 |
| `let` | 63 |
| `print` | 64 |
| `goto` | 65 |
| `if` | 66 |
| `end` | 67 |

💡 **Dica de prova:** repare no padrão de faixas — delimitadores (0x), atribuição (11), aritméticos (2x), relacionais (3x), identificadores/constantes (4x/5x), palavras reservadas (6x). Isso **não é decoreba aleatória**: a faixa numérica já denuncia a categoria do token, o que facilita tanto memorizar quanto o próprio compilador processar de forma mais compacta.

---

## 7. Padrão de Resolução de Exercícios (como os gabaritos são montados)

Toda solução de exercício SIMPLE segue **3 partes**:

**Parte 1 — Código-fonte SIMPLE**
Escrever o programa numerado, respeitando a lógica pedida.

**Parte 2 — Tabela de Símbolos**
Uma tabela sequencial (código 00, 01, 02...) listando, **na ordem em que aparecem no texto**, cada número de linha, cada identificador e cada constante numérica **distinta** (valores repetidos reaproveitam o mesmo código).

**Parte 3 — Lista de Tokens**
Cada token do texto, na ordem, no formato:
```
[CLASSE, VALOR, (linha, coluna)]  // comentário do que é
```
- `CLASSE` = código da tabela da seção 6.
- `VALOR` = código na tabela de símbolos (vazio para tokens simples: operadores, palavras reservadas, delimitadores).
- `(linha, coluna)` = posição real no texto fonte, contando **cada caractere/espaço**.
- Ao final de **cada linha** do programa (exceto a última, que tem `end`): token **ENTER** → `[10, , (linha, coluna)]`.
- Ao final do **programa inteiro** (logo após o token `end`, sem ENTER entre eles): token **ETF** → `[03, , (linha, coluna)]`.

---

## 8. Checklist Rápido Antes da Prova

- [ ] Sei os 3 componentes de um token (classe, valor, posição)?
- [ ] Sei a diferença entre token simples e token com argumento?
- [ ] Sei decorar as faixas de código (0x delimitador, 11 atribuição, 2x aritmético, 3x relacional, 41/51 id/constante, 6x palavra reservada)?
- [ ] Sei que a tabela de símbolos reaproveita código para valores repetidos?
- [ ] Sei que ETF vem logo após `end`, sem ENTER entre eles?
- [ ] Sei explicar por que léxico ≠ garantia de sintaxe correta (linguagem regular vs. livre de contexto)?

---

## 9. Exercícios Progressivos + Gabarito

### Nível 1 — Conceitual

**Questão 1.** Explique com suas palavras o que é a "classe" de um token e por que ela é mais importante que o "valor" para o analisador sintático.

<details>
<summary>🔓 Gabarito</summary>

A **classe** indica o *tipo* do token (ex: identificador, operador de soma, palavra reservada `if`). Para o **analisador sintático**, o que importa é a estrutura gramatical da linguagem — ele precisa saber que ali existe "um identificador" ou "um operador relacional", não qual é o nome exato da variável ou o valor exato da constante. O **valor** (ex: qual identificador, qual número) só é necessário mais tarde, nas fases semânticas e de geração de código, quando o compilador precisa saber *qual* variável está sendo referenciada. Por isso, para palavras reservadas, a classe sozinha já basta — nem se passa valor.
</details>

---

### Nível 2 — Comparativo

**Questão 2.** Compare: por que os operadores relacionais (`==`, `!=`, etc.) e os aritméticos (`+`, `-`, etc.) têm faixas de código diferentes (3x e 2x)? O que isso facilita?

<details>
<summary>🔓 Gabarito</summary>

Separar em faixas diferentes permite que o compilador (ou até um ser humano lendo a lista de tokens) identifique **a categoria do token só pela dezena do código**, sem precisar de uma tabela de consulta extra. Um código que começa com "2" já indica "isto é aritmético", um que começa com "3" indica "isto é relacional". Isso é útil, por exemplo, na fase de **análise sintática**, onde a gramática trata operadores aritméticos e relacionais de formas diferentes (precedência, tipo de expressão resultante); ter as faixas separadas agiliza essa checagem. Também torna a tabela de tokens mais legível para fins didáticos e de depuração.
</details>

---

### Nível 3 — Computacional Simples

**Questão 3.** Dado o trecho `10 let a = 5`, sendo esta a **primeira linha** do programa, monte a lista de tokens no formato `[classe, valor, (linha,coluna)]`.

<details>
<summary>🔓 Gabarito</summary>

Tabela de símbolos parcial (apenas o necessário para esta linha):
```
00: 10
01: a
02: 5
```

Contagem de colunas em `10 let a = 5`:
```
1:'1' 2:'0' 3:' ' 4:'l' 5:'e' 6:'t' 7:' ' 8:'a' 9:' ' 10:'=' 11:' ' 12:'5'
```

Lista de tokens:
```
[51, 00, (01, 01)] // 10
[63, , (01, 04)]   // let
[41, 01, (01, 08)] // a
[11, , (01, 10)]   // =
[51, 02, (01, 12)] // 5
[10, , (01, 13)]   // ENTER
```
</details>

---

### Nível 4 — Computacional Completo

**Questão 4.** Escreva um programa SIMPLE que leia dois números e imprima o maior deles. Depois monte a tabela de símbolos completa e a lista de tokens.

<details>
<summary>🔓 Gabarito</summary>

**Código-fonte:**
```
10 input a
15 input b
20 if a >= b goto 30
25 let a = b
30 print a
35 end
```

**Tabela de símbolos:**
```
Código  Valor
00      10
01      a
02      15
03      b
04      20
05      30
06      25
07      35
```

**Lista de tokens:**
```
[51, 00, (01, 01)] // 10
[62, , (01, 04)]   // input
[41, 01, (01, 10)] // a
[10, , (01, 11)]   // ENTER

[51, 02, (02, 01)] // 15
[62, , (02, 04)]   // input
[41, 03, (02, 10)] // b
[10, , (02, 11)]   // ENTER

[51, 04, (03, 01)] // 20
[66, , (03, 04)]   // if
[41, 01, (03, 07)] // a
[35, , (03, 09)]   // >=
[41, 03, (03, 12)] // b
[65, , (03, 14)]   // goto
[51, 05, (03, 19)] // 30
[10, , (03, 21)]   // ENTER

[51, 06, (04, 01)] // 25
[63, , (04, 04)]   // let
[41, 01, (04, 08)] // a
[11, , (04, 10)]   // =
[41, 03, (04, 12)] // b
[10, , (04, 13)]   // ENTER

[51, 05, (05, 01)] // 30
[64, , (05, 04)]   // print
[41, 01, (05, 10)] // a
[10, , (05, 11)]   // ENTER

[51, 07, (06, 01)] // 35
[67, , (06, 04)]   // end
[03, , (06, 07)]   // ETF
```

**Por que funciona:** se `a >= b`, o programa já pula direto para imprimir `a` (que é o maior ou igual). Se `a < b`, cai na linha 25, que substitui `a` pelo valor de `b` antes de imprimir — garantindo que sempre imprimimos o maior valor.
</details>

---

### Nível 5 — Síntese (estilo ENADE)

**Questão 5.** Um colega afirma que "a análise léxica já garante que o programa está sintaticamente correto". Você concorda? Justifique usando os conceitos de linguagem regular vs. linguagem livre de contexto.

<details>
<summary>🔓 Gabarito</summary>

**Discordo.** A análise léxica garante apenas que os *símbolos individuais* do programa (tokens) pertencem ao vocabulário válido da linguagem — ou seja, que o texto fonte é uma sequência de palavras de uma **linguagem regular** (reconhecível por um autômato finito). Isso significa que, por exemplo, `if`, `x`, `123` e `<=` são todos tokens válidos.

Porém, isso **não garante nada sobre a ordem ou a estrutura** em que esses tokens aparecem. Por exemplo, a sequência `if if if goto goto` é composta inteiramente por tokens válidos (léxico correto), mas não forma um comando válido da linguagem. Verificar se a sequência de tokens forma uma sentença gramaticalmente válida é responsabilidade do **analisador sintático**, que trata o programa como uma sentença de uma **linguagem livre de contexto** (reconhecível por um autômato de pilha / gramática livre de contexto), capaz de expressar estruturas aninhadas e recursivas que uma linguagem regular não consegue representar.

Em resumo: léxico correto ⇏ sintaxe correta. São fases complementares e sequenciais, cada uma responsável por um nível diferente da estrutura da linguagem.
</details>

---

## 10. Próximos Passos

- Praticar simulando manualmente a execução do programa SIMPLE antes de montar a tabela de tokens (evita erros de lógica).
- Contar colunas caractere a caractere com atenção — é o erro mais comum em prova.
- Revisar a tabela de códigos (seção 6) até decorar de cor.
