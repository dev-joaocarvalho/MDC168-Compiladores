# 📘 Compiladores — Guia Completo para a Primeira Avaliação

> Material de estudo do zero até o nível de prova. Se você entender tudo neste arquivo, na ordem em que ele está escrito, você terá base para resolver **qualquer** variação das questões da Primeira Avaliação (SIMPLE, FIRST/FOLLOW, recursão à esquerda, fatoração, escopo, precedência de operadores, análise preditiva tabular e questões conceituais).

**Como usar este guia:** leia na ordem. Cada seção depende da anterior. No final tem um **mapa questão → tópico** e um **checklist**.

---

## Índice

1. [O que é um compilador](#1-o-que-é-um-compilador)
2. [Tipos de tradutores de linguagem](#2-tipos-de-tradutores-de-linguagem)
3. [As fases internas de um compilador](#3-as-fases-internas-de-um-compilador)
4. [Análise léxica: tokens e tabela de símbolos](#4-análise-léxica-tokens-e-tabela-de-símbolos)
5. [Gramáticas livres de contexto: o básico](#5-gramáticas-livres-de-contexto-o-básico)
6. [Conjuntos FIRST e FOLLOW](#6-conjuntos-first-e-follow)
7. [Recursão à esquerda e fatoração à esquerda](#7-recursão-à-esquerda-e-fatoração-à-esquerda)
8. [Análise sintática descendente preditiva (LL(1))](#8-análise-sintática-descendente-preditiva-ll1)
9. [Análise por precedência de operadores](#9-análise-por-precedência-de-operadores)
10. [Análise semântica: tabela de símbolos e escopo](#10-análise-semântica-tabela-de-símbolos-e-escopo)
11. [A linguagem SIMPLE](#11-a-linguagem-simple)
12. [Tópicos conceituais (Enade/Poscomp)](#12-tópicos-conceituais-enadeposcomp)
13. [Mapa: questão da prova → tópico necessário](#13-mapa-questão-da-prova--tópico-necessário)
14. [Checklist final](#14-checklist-final)

---

## 1. O que é um compilador

Programadores pensam em **linguagens de alto nível** (próximas da linguagem humana: `if`, `while`, nomes de variáveis). Computadores só entendem **linguagem de máquina** (sequências de 0s e 1s).

Um **compilador** é um programa que faz essa tradução:

```
programa fonte (alto nível)  →  [ COMPILADOR ]  →  programa objeto (máquina/simbólica)
```

Depois, o **programa objeto** roda separadamente, recebendo dados de entrada e produzindo resultados:

```
dados de entrada  →  [ PROGRAMA OBJETO ]  →  resultados
```

> 💡 **Por que isso importa pra prova:** a Questão 4 do Enade (2021) e outras conceituais cobram exatamente essa distinção — compilador traduz, o programa objeto **executa**.

---

## 2. Tipos de tradutores de linguagem

| Tradutor | O que faz |
|---|---|
| **Montador (assembler)** | Traduz linguagem simbólica (assembly) → linguagem de máquina, geralmente **1 instrução para 1 instrução** |
| **Macro-assembler** | Igual ao montador, mas **1 instrução simbólica pode virar várias** instruções de máquina |
| **Compilador** | Traduz linguagem de **alto nível** → linguagem simbólica ou de máquina |
| **Pré-compilador / pré-processador / filtro** | Traduz entre **duas linguagens de alto nível** (ex.: Fortran IV Nível G IBM → Fortran IV padrão ANSI) |
| **Interpretador** | Não gera código de máquina. Executa **diretamente** o efeito do programa (usa código intermediário + dados → produz resultados na hora) |

> 💡 **Pegadinha clássica de prova:** expressões regulares **geram** linguagens — elas **não são reconhecedores**. Quem reconhece:
> - **Linguagens regulares** → Autômato Finito (determinístico ou não)
> - **Linguagens livres de contexto** → Autômato de Pilha

---

## 3. As fases internas de um compilador

Um compilador é dividido em duas grandes etapas: **Análise** (entende o programa fonte) e **Síntese** (gera o programa objeto).

```
programa fonte
      ↓
┌─────────────────── ANÁLISE ───────────────────┐
│  1. Analisador léxico                          │
│  2. Analisador sintático                       │
│  3. Analisador semântico                       │
└─────────────────────────────────────────────────┘
      ↓
┌─────────────────── SÍNTESE ───────────────────┐
│  4. Gerador de código intermediário            │
│  5. Otimizador de código                       │
│  6. Gerador de código objeto                   │
└─────────────────────────────────────────────────┘
      ↓
programa objeto
```

Duas estruturas de apoio atravessam **todas** as fases:
- **Tabelas** (principalmente a tabela de símbolos)
- **Atendimento a erros** — cada fase pode detectar erros, mas **deve continuar a análise** mesmo depois de encontrar um erro, para reportar o máximo de problemas de uma vez.

### O que cada fase faz

| Fase | Função | Erros que detecta |
|---|---|---|
| **Léxica** | Lê caractere a caractere e agrupa em *tokens* (palavras reservadas, identificadores, constantes, operadores). Descarta espaços e comentários. | Caractere/símbolo fora do alfabeto da linguagem |
| **Sintática** | Verifica se a sequência de tokens forma uma estrutura gramatical válida (usa a gramática da linguagem) | Estrutura gramatical incorreta (ex.: `if` sem `then`) |
| **Semântica** | Verifica se as estruturas **fazem sentido** — tipos compatíveis, variáveis declaradas, etc. | Tipos incompatíveis, variável não declarada, uso antes da inicialização |
| **Geração de código intermediário** | Gera uma sequência de código genérico a partir da árvore sintática | — |
| **Otimização** | Melhora o código intermediário (velocidade/memória) | — |
| **Geração de código objeto** | Produz o código final, seleciona registradores, aloca memória | — |

> 💡 **Pegadinha de prova (muito cobrada):**
> - **Análise descendente (top-down)**: percorre a árvore sintática da **raiz para as folhas** (começa no símbolo inicial da gramática e vai gerando até chegar nos terminais).
> - **Análise ascendente (bottom-up)**: percorre da **folha para a raiz** (parte dos símbolos terminais lidos e vai reduzindo até o símbolo inicial).
>
> As provas **invertem essa definição de propósito** numa assertiva. Decore assim: "descendente desce da raiz; ascendente sobe das folhas".

---

## 4. Análise léxica: tokens e tabela de símbolos

### O que é um token
Um **token** (símbolo léxico) é a menor unidade com significado no texto do programa. Exemplos: palavra reservada (`while`), identificador (`I`), constante (`100`), operador (`<`, `:=`).

Cada token é representado por até 3 informações:
1. **Classe** — o tipo (identificador, operador, palavra reservada...)
2. **Valor** — depende da classe (ex.: para uma constante numérica, o valor é o próprio número; identificadores e constantes guardam um **índice** para a tabela onde o valor real está)
3. **Posição** — linha e coluna onde ocorreu (usado para apontar erros)

**Tokens simples** (palavras reservadas, operadores, delimitadores) não precisam de valor — a classe já descreve tudo.
**Tokens com argumento** (identificadores, constantes) têm um valor associado.

### Exemplo
```
while I < 100 do I := J + I;
```
vira a sequência de tokens:
```
[while, ] [id, 7] [<, ] [cte, 13] [do, ] [id, 7] [:=, ] [id, 12] [+, ] [id, 7] [;, ]
```

### Tabela de símbolos
É criada **durante a análise léxica**, quando identificadores são reconhecidos. Guarda os nomes (variáveis, funções, parâmetros) e seus atributos (tipo, escopo, etc.). Cada vez que um identificador aparece, a tabela é consultada: se não existe, é inserido; se já existe, reaproveita a entrada.

---

## 5. Gramáticas livres de contexto: o básico

Uma **gramática livre de contexto (GLC)** define como as sentenças válidas de uma linguagem são formadas. É representada como `G = (V, T, P, S)`:
- **V** = conjunto de não-terminais (variáveis, escritas em maiúsculo: `A`, `B`, `S`...)
- **T** = conjunto de terminais (símbolos da linguagem: `a`, `b`, `+`, `(`...)
- **P** = conjunto de produções (regras de substituição: `A → aA`)
- **S** = símbolo inicial

**Notação `|`**: significa "ou". `A → aA | b` quer dizer "A pode virar aA OU pode virar b".

**Palavra vazia**: representada por **ε** (épsilon) — significa que o não-terminal pode "desaparecer" (não gerar nenhum símbolo).

> 💡 Você não precisa saber provar formalmente essas definições para a prova — precisa saber **aplicar mecanicamente** os algoritmos das seções seguintes, que são construídos em cima de uma GLC dada no enunciado.

---

## 6. Conjuntos FIRST e FOLLOW

Servem para o analisador saber, **olhando só o próximo símbolo da entrada**, qual produção aplicar (é a base da análise preditiva, seção 8).

### FIRST(X) — "quais terminais podem começar uma cadeia derivada de X"

Regras:
1. Se **X é terminal**: `FIRST(X) = {X}`.
2. Se existe produção **X → ε**: adicione ε a `FIRST(X)`.
3. Se existe produção **X → Y₁Y₂...Yₙ**:
   - Adicione `FIRST(Y₁) − {ε}` a `FIRST(X)`.
   - Se ε ∈ `FIRST(Y₁)`: adicione também `FIRST(Y₂) − {ε}`, e continue olhando Y₃, Y₄... enquanto os anteriores tiverem ε em seu FIRST.
   - Se **todos** os Yᵢ da produção têm ε em seu FIRST, então ε também entra em `FIRST(X)`.

### FOLLOW(A) — "quais terminais podem aparecer logo depois de A numa derivação"

Regras:
1. `$` (fim de cadeia) ∈ `FOLLOW(S)`, onde S é o símbolo inicial.
2. Se existe produção **A → αBβ** (B no meio ou no fim seguido de algo): tudo em `FIRST(β) − {ε}` entra em `FOLLOW(B)`.
3. Se existe produção **A → αB** (B é o último símbolo), **ou** `A → αBβ` onde **ε ∈ FIRST(β)**: tudo que está em `FOLLOW(A)` entra também em `FOLLOW(B)`.

### Método prático (como calcular sem se perder)
1. Calcule primeiro **todos os FIRST**, dos não-terminais "mais simples" (sem recursão) para os mais complexos.
2. Para o FOLLOW, **percorra cada não-terminal B** e pergunte: "em quais produções da gramática B aparece do lado direito?" Para cada ocorrência, aplique as regras 2 e 3.
3. **Repita passando por todas as produções de novo** até que nenhum conjunto mude mais (às vezes FOLLOW depende de outro FOLLOW que ainda não estava completo).

### Exemplo completo resolvido
```
G = ({S, A, B, C}, {a, b, c, d}, P, S)
P = {S → AC | CdB | Ba
     A → aA | BC
     B → bB | CB | ε
     C → cC | ε}
```

**Calculando FIRST:**
- `FIRST(C) = {c, ε}` — direto das produções `cC` e `ε`.
- `FIRST(B) = {b, c, ε}` — de `bB` vem `b`; de `CB` herda `FIRST(C)−{ε} = {c}` e, como ε∈FIRST(C), também herda FIRST(B) (recursivo, não trava); do próprio `ε`.
- `FIRST(A) = {a, b, c, ε}` — de `aA` vem `a`; de `BC` herda `FIRST(B)−{ε} = {b,c}`, e como ε∈FIRST(B), herda também `FIRST(C) = {c, ε}`.
- `FIRST(S) = {a, b, c, d}` — de `AC` herda FIRST(A) (sem ε, pois se A→ε e C→ε ainda restaria d de CdB e a de Ba, mas repare que S não tem produção ε própria, então cuidado: aqui olhamos symbol a symbol); de `CdB` vem `c` (de C) e `d` (se C→ε); de `Ba` vem FIRST(B) e, se B→ε, o `a`.

**Calculando FOLLOW:**
- `FOLLOW(S) = {$}` (regra 1).
- `FOLLOW(C)`: aparece em `S→AC` (final → herda FOLLOW(S) = {$}); em `S→CdB` (seguido de `d` → adiciona `d`); em `A→BC` (final → herda FOLLOW(A)); em `B→CB` (seguido de B, adiciona FIRST(B)−{ε}={b,c}, e como ε∈FIRST(B), herda também FOLLOW(B)).
- `FOLLOW(B)`: aparece em `S→CdB` (final → herda FOLLOW(S)={$}); em `A→BC` (seguido de C, adiciona FIRST(C)−{ε}={c}, e como ε∈FIRST(C), herda FOLLOW(A)); em `B→CB` (final → herda FOLLOW(B), recursivo).
- `FOLLOW(A)`: aparece em `S→AC` (seguido de C, adiciona FIRST(C)−{ε}={c}, e herda FOLLOW(S)={$} pois ε∈FIRST(C)).

> ⚠️ **Erro mais comum:** esquecer de "herdar" o FOLLOW do lado esquerdo quando o não-terminal está no **final** da produção, ou quando tudo que vem depois dele pode virar ε.

---

## 7. Recursão à esquerda e fatoração à esquerda

Os métodos de análise **descendente** (seção 8) não funcionam se a gramática tiver recursão à esquerda ou ambiguidade por prefixo comum. Por isso, **antes** de construir a tabela LL(1), a gramática precisa passar por essas duas transformações.

### 7.1 Eliminação de recursão à esquerda (direta)

Uma gramática tem recursão à esquerda direta quando:
```
A → Aα₁ | Aα₂ | ... | Aαₘ | β₁ | β₂ | ... | βₙ
```
onde nenhum βᵢ começa com A.

**Transformação:**
```
A  → β₁A' | β₂A' | ... | βₙA'
A' → α₁A' | α₂A' | ... | αₘA' | ε
```

**Como aplicar sem errar:**
1. Separe as alternativas de A em dois grupos: as que **começam com A** (recursivas) e as que **não começam** (não-recursivas).
2. Nas recursivas, tire o A da frente — o que sobra é um `αᵢ`.
3. Nas não-recursivas, o que sobra inteiro é um `βᵢ`.
4. Monte `A → β₁A' | β₂A' | ...` (cada β ganha A' no final).
5. Monte `A' → α₁A' | α₂A' | ... | ε` (cada α ganha A' no final, e sempre acrescente ε).

**Exemplo:**
```
B → a | BC | DC
```
- Não-recursivas (β): `a`, `DC`
- Recursivas (α, tirando o B da frente de `BC`): `C`

Resultado:
```
B  → aB' | DCB'
B' → CB' | ε
```

### 7.2 Fatoração à esquerda

Quando duas ou mais produções de um não-terminal **começam com o mesmo prefixo**, o analisador preditivo não sabe qual escolher. A fatoração adia a decisão.

```
A → αβ₁ | αβ₂
```
vira
```
A  → αA'
A' → β₁ | β₂
```

**Como aplicar sem errar:**
1. Identifique o **maior prefixo comum** entre as alternativas.
2. Tire esse prefixo e coloque um novo não-terminal (`A'`) no lugar.
3. As "sobras" de cada alternativa (o que vinha depois do prefixo) viram as produções de `A'`. Se uma alternativa era **exatamente** igual ao prefixo (nada sobrou), a sobra dela é **ε**.
4. **Repita o processo** se, depois de fatorar, `A'` ainda tiver alternativas com prefixo comum entre si.

**Exemplo:**
```
C → xy | xDB
```
Prefixo comum: `x`. Sobras: `y` e `DB`.
```
C  → xC'
C' → y | DB
```

> ⚠️ **Dica de prova:** monte a transformação **do zero no rascunho**, nunca tente "reconhecer" se uma assertiva pronta está certa só de olhar — é fácil cair em pegadinha de faltar o ε ou trocar a ordem.

---

## 8. Análise sintática descendente preditiva (LL(1))

Essa é a técnica que junta os tópicos 6 e 7: usar uma **tabela M[não-terminal, terminal]** para decidir, sem tentativa e erro, qual produção aplicar a cada passo, olhando 1 símbolo à frente (por isso "LL(1)": **L**eitura da esquerda pra direita, derivação mais à esquerda, **1** símbolo de lookahead).

### 8.1 Construindo a tabela M

Antes de tudo: **elimine recursão à esquerda e faça fatoração à esquerda** (seção 7), depois **calcule FIRST e FOLLOW** (seção 6). Só então construa a tabela:

Para cada produção **A → α** da gramática:
- Para **cada terminal `t` em `FIRST(α)`**: coloque `A → α` na célula `M[A, t]`.
- Se **ε ∈ FIRST(α)**: para **cada terminal `t` em `FOLLOW(A)`** (incluindo `$` se aplicável): coloque `A → α` na célula `M[A, t]`.

As linhas de **terminais** da tabela (usadas no algoritmo de execução) recebem "**sinc**" (sincroniza) na coluna do próprio terminal — significa "casa esse símbolo da pilha com a entrada e avança os dois".

Células vazias = erro. O enunciado da prova sempre define uma rotina de **recuperação em modo pânico** (ex.: "insere token X na entrada" ou "descarta símbolo da entrada").

### 8.2 Exemplo completo (gramática sem recursão nem prefixo comum — direto ao ponto)
```
G = ({A,B,C,D,E,F,G}, {x,y,z}, P, A)
P = {A → xB
     B → DC
     C → xC | yC | zC | ε
     D → yE
     E → GF
     F → xF | yF | zF | ε
     G → z}
```

FIRST e FOLLOW:
```
FIRST(A)={x}          FOLLOW(A)={$}
FIRST(B)={y}          FOLLOW(B)={$}
FIRST(C)={x,y,z,ε}    FOLLOW(C)={$}
FIRST(D)={y}          FOLLOW(D)={x,y,z,$}
FIRST(E)={z}          FOLLOW(E)={x,y,z,$}
FIRST(F)={x,y,z,ε}    FOLLOW(F)={x,y,z,$}
FIRST(G)={z}          FOLLOW(G)={x,y,z,$}
```

Tabela M:

|   | x | y | z | $ |
|---|---|---|---|---|
| **A** | A→xB | | | sinc |
| **B** | | B→DC | | sinc |
| **C** | C→xC | C→yC | C→zC | C→ε |
| **D** | sinc | D→yE | sinc | sinc |
| **E** | sinc | sinc | E→GF | sinc |
| **F** | F→xF | F→yF | F→zF | F→ε |
| **G** | sinc | sinc | G→z | sinc |

> ⚠️ **Repare:** como `ε ∈ FIRST(C)` e `$ ∈ FOLLOW(C)`, a célula `M[C,$]` recebe `C→ε`. Mesma lógica em F. **Esse é o erro mais comum da questão extra: esquecer essas células "herdadas" do FOLLOW.**

### 8.3 Executando a análise (algoritmo dirigido por pilha)

Pilha inicial: `$A` (A = símbolo inicial, no topo).

A cada passo, seja **X** o topo da pilha e **a** o símbolo atual da entrada:

1. **X é terminal:**
   - Se X = a: **desempilha X e avança a entrada** (isso é o "sinc").
   - Se X ≠ a: erro.
2. **X é não-terminal:** consulte `M[X, a]`:
   - Se existe `X → Y₁Y₂...Yₙ`: desempilha X, empilha `YₙYₙ₋₁...Y₁` (**ordem invertida**, para Y₁ ficar no topo).
   - Se existe `X → ε`: apenas desempilha X.
   - Se a célula está vazia: aplica a recuperação de erro do enunciado.
3. Repete até pilha = `$` e entrada = `$` → **aceito**.

**Monte sempre uma tabela de 3 colunas: Pilha | Entrada | Derivação**, exatamente como pedido nas provas. Exemplo de início (entrada `x y z + x z / * - y +`, gramática de expressão com produções A→CD, D→ABD|ε etc.):

| Pilha | Entrada | Derivação |
|---|---|---|
| $A | x y z + ... $ | A → CD |
| $D C | x y z + ... $ | C → x |
| $D x | x y z + ... $ | sinc |
| $D | y z + ... $ | D → ABD |
| ... | ... | ... |

> ⚠️ **Erro mais comum:** esquecer que "sinc" também **avança a entrada** (não só desempilha), e esquecer de inverter a ordem ao empilhar o lado direito de uma produção.

---

## 9. Análise por precedência de operadores

Técnica **ascendente** (bottom-up), usada principalmente para expressões aritméticas/lógicas. Funciona com uma **tabela de precedência** entre pares de símbolos terminais, com três relações possíveis:

- **`<`** — o símbolo do topo da pilha tem precedência **menor**: **empilha** o símbolo da entrada, avança a entrada.
- **`>`** — o símbolo do topo tem precedência **maior**: **reduz** (desempilha o *handle* — o trecho que bate com o lado direito de uma produção — e empilha o não-terminal correspondente). **Não avança a entrada.**
- **`=`** — só ocorre entre parênteses (`(` e `)`): empilha e avança a entrada (como o `<`), mas sinaliza combinação de parênteses.
- **espaço em branco** — erro, tratado pela rotina definida no enunciado.

### Algoritmo passo a passo
1. Pilha começa com `$`. Compare o **topo da pilha (ignorando não-terminais)** com o **símbolo atual da entrada** na tabela.
2. Aplique a relação (`<`, `>`, `=` ou erro) conforme acima.
3. Repita até a pilha ficar `$A` (símbolo inicial) e a entrada `$` → **aceita**.

### Como identificar o *handle* na hora de reduzir
Olhe o topo da pilha (ignorando os não-terminais que já foram reduzidos) e ache qual produção da gramática "bate" com aquele trecho. Ex.: se a pilha tem `...A + A` e a relação é `>`, e a gramática tem `A → A + B`, você reduz usando essa produção (o `A` da direita, antes de reduzir, ainda está representado pelo não-terminal que acabou de ser formado).

### Exemplo (início da simulação — entrada `a + b * c * d + e`)
```
G: A → A+B | B
   B → B*C | C
   C → a|b|c|d|e
```

| Pilha | Relação | Entrada | Ação |
|---|---|---|---|
| $ | < | a + b * c * d + e $ | empilha a |
| $a | > | + b * c * d + e $ | reduz C → a |
| $A | < | + b * c * d + e $ | empilha + |
| $A+ | < | b * c * d + e $ | empilha b |
| $A+b | > | * c * d + e $ | reduz C → b |
| ... | ... | ... | ... |

> 💡 **Regra prática de parênteses:** `(` sempre é `<` tudo à direita, `)` sempre é `>` tudo à esquerda, exceto `( = )`.
>
> ⚠️ **Dica de prova:** copie a mensagem de erro **exatamente como está no enunciado** (erro 1, erro 2...) — a banca define isso caso a caso e espera que você reproduza literalmente.

---

## 10. Análise semântica: tabela de símbolos e escopo

### Por que a análise sintática não basta
A gramática livre de contexto consegue verificar a **forma** do programa, mas não consegue verificar regras como "toda variável deve ser declarada" ou "os tipos são compatíveis". Isso é trabalho da **análise semântica**, que usa a **árvore sintática** e a **tabela de símbolos** gerada durante a análise.

### Tabela de símbolos
Cada entrada = a declaração de um nome, com atributos (tipo, classe, escopo, tamanho). Uma tabela de símbolos é criada **para cada escopo/bloco** (ex.: uma para o programa principal, outra para cada procedimento).

### Escopo estático (léxico)
- A visibilidade de uma variável é definida **pela posição do código no texto-fonte** — não importa quem chamou quem, importa **onde o bloco está escrito**.
- Usado pela maioria das linguagens modernas (C, Pascal, Java...).
- **Como resolver:** para achar a declaração de uma variável, comece no escopo atual e **suba na hierarquia textual** (bloco que contém o bloco atual, e assim por diante) até achar uma declaração com aquele nome. Se um bloco mais interno declara uma variável com o mesmo nome de um bloco externo, a variável externa fica **oculta** dentro do bloco interno (mas continua existindo fora dele).

### Escopo dinâmico
- A visibilidade segue a **sequência de chamadas em tempo de execução** (a pilha de ativação), **não** a posição no texto.
- Usado por APL, SNOBOL4, LISP antigo (raro em linguagens modernas — mas cai bastante na prova em formato de pseudocódigo Java "fingindo" ter escopo dinâmico).

**Como resolver questões de escopo dinâmico (passo a passo):**
1. **Monte a pilha de ativação** seguindo a ordem exata da chamada dada no enunciado (ex.: `main chama sub3; sub3 chama sub4; sub4 chama sub2`).
2. Empilhe cada função com suas variáveis locais, na ordem de chamada.
3. As variáveis visíveis, **na função que está executando por último (topo da pilha)**, são:
   - Todas as variáveis **locais** dela.
   - Mais as variáveis dos ativadores abaixo na pilha **que não têm o mesmo nome** de nenhuma variável mais próxima do topo (se duas funções na pilha declaram uma variável de mesmo nome, **só a mais próxima do topo é visível** — a outra fica oculta).

**Exemplo resolvido:**
```java
sub1() { int a, y, z, w; }
sub2() { int a, b, z, w; }
sub3() { int a, b, c, w; }
sub4() { int a, b, c, d; }
main()  { int x, y, z, w; }
```
Sequência: `main → sub3 → sub4 → sub2` (sub2 é a que está executando agora, no topo).

Pilha (de baixo para cima): `main(x,y,z,w)` → `sub3(a,b,c,w)` → `sub4(a,b,c,d)` → `sub2(a,b,z,w)`

Analisando nome por nome, de cima (mais recente) para baixo:
- `sub2`: a, b, z, w → todas visíveis (são locais)
- `sub4`: a e b já apareceram em sub2 → ocultos; c e d são novos → visíveis
- `sub3`: a, b, w já apareceram acima → ocultos; c já apareceu em sub4 → oculto → **nada novo**
- `main`: x é novo → visível; y, z, w já apareceram acima → ocultos

**Resultado: x (de main), a, b, z, w (de sub2), c, d (de sub4).**

> 💡 **Truque para não errar:** monte uma tabela com uma coluna por função na pilha (da mais antiga à mais nova) e, para cada nome de variável, marque **só na coluna mais à direita (mais recente)** onde ele aparece. É mecânico e rápido.

### Ambiente de referenciamento
É o conjunto de **todos os nomes visíveis** numa instrução — em escopo estático, é formado pela variável local + todas as variáveis visíveis nos escopos textuais superiores; em escopo dinâmico, é formado pela variável local + todas as variáveis visíveis em todos os **subprogramas ativos** (que já começaram a executar mas ainda não terminaram).

---

## 11. A linguagem SIMPLE

Linguagem didática usada na Questão 1. Regras gerais:
- Cada instrução = **número de linha** + **comando**.
- Números de linha em **ordem crescente** (não precisam ser consecutivos).
- Variáveis: **uma única letra minúscula**, sempre do tipo inteiro.
- Operadores aritméticos: `+  -  *  /  %` (resto da divisão)
- Operadores relacionais: `>  >=  <  <=  ==  !=`

### Comandos

| Comando | Exemplo | O que faz |
|---|---|---|
| `input` | `10 input a` | Lê um inteiro do usuário e guarda em `a` |
| `let` | `40 let a = b + c` | Atribuição |
| `print` | `60 print a` | Exibe o valor de `a` |
| `goto` | `50 goto 30` | Pula incondicionalmente para a linha 30 |
| `if` | `15 if a <= 0 goto 55` | Se a condição é verdadeira, pula para a linha indicada; senão, segue para a próxima linha |
| `rem` | `10 rem comentário` | Comentário — ignorado pelo compilador |
| `end` | `99 end` | Encerra o programa |

### Receita para escrever qualquer programa SIMPLE
1. **Escreva primeiro em português/pseudocódigo** o algoritmo (Euclides para MDC, laço para MMC, recorrência para sequências, laço para número perfeito, etc.).
2. **Valide as entradas primeiro**, logo após cada `input`, com `if <condição inválida> goto <label de erro>`.
3. Numere de **10 em 10** (ou 5 em 5) — assim sobra espaço pra inserir linhas depois, e os `goto` sempre apontam pra números redondos.
4. Traduza todo `while <condição> { corpo }` para o padrão:
   ```
   30 if <condição contrária/parada> goto <fim>
   ...corpo do laço...
   goto 30
   99 end
   ```
   (teste no topo, condição **invertida** pulando pra fora do laço quando deve parar, e um `goto` de volta ao teste no final do corpo)
5. **Sempre termine com `end`.**
6. Trate erro num bloco separado, geralmente pouco antes do `print` final.

### Exemplo comentado (MDC pelo Algoritmo de Euclides)
```
10 input a
15 if a <= 0 goto 55      ← valida entrada a
20 input b
25 if b <= 0 goto 55      ← valida entrada b
30 if b == 0 goto 60      ← topo do laço: se b=0, MDC=a, vai imprimir
35 let r = a % b
40 let a = b
45 let b = r
50 goto 30                ← repete o laço
55 let a = -1             ← tratamento de erro
60 print a
65 end
```

**Esse padrão (validar → inicializar → laço com teste no topo → imprimir → bloco de erro separado) resolve MDC, MMC, Fibonacci/Tribonacci, número perfeito, primo, fatorial e qualquer variação parecida.**

---

## 12. Tópicos conceituais (Enade/Poscomp)

Questões de múltipla escolha que caem nas provas mais recentes (2024.2, 2025.2). Pontos que mais aparecem:

- **Hierarquia de Chomsky × reconhecedores:**
  - Linguagens **regulares** → reconhecidas por **Autômatos Finitos** (determinísticos ou não).
  - Linguagens **livres de contexto** → reconhecidas por **Autômatos de Pilha**.
  - **Expressões regulares geram linguagens, não reconhecem** (não são "reconhecedores" — pegadinha comum).
- **Top-down × bottom-up:** descendente vai da raiz às folhas; ascendente vai das folhas à raiz (as provas trocam isso propositalmente).
- **Erro por fase:**
  - Léxico → símbolo fora do alfabeto da linguagem.
  - Sintático → estrutura gramatical incorreta.
  - Semântico → tipos incompatíveis, variável não declarada — **mesmo com sintaxe perfeita**.
- **Conflitos em tabelas de precedência de operadores:**
  a) ausência de relação de precedência entre o topo da pilha e a entrada;
  b) o analisador espera um *handle* no topo mas não existe produção correspondente.
- **Fatoração e recursão à esquerda:** necessárias antes de construir um analisador preditivo (LL(1)) — uma gramática com prefixos comuns ou recursão à esquerda **não pode** ser processada diretamente por um analisador preditivo tabular.
- **Esquema de tradução:** é uma **gramática livre de contexto com ações semânticas embutidas** no lado direito das produções (não é um grafo, nem uma técnica de recuperação de erro).

> 💡 **Como estudar essas questões:** não decore a resposta, **releia a definição e desenhe/calcule você mesmo** antes de marcar. A pegadinha típica é inverter dois conceitos parecidos (ex.: FIRST com FOLLOW, top-down com bottom-up).

---

## 13. Mapa: questão da prova → tópico necessário

| Questão típica | Seções para revisar |
|---|---|
| Programa em linguagem SIMPLE | [Seção 11](#11-a-linguagem-simple) |
| Calcular FIRST/FOLLOW ou julgar assertivas sobre eles | [Seção 6](#6-conjuntos-first-e-follow) |
| Fatoração à esquerda / eliminação de recursão à esquerda | [Seção 7](#7-recursão-à-esquerda-e-fatoração-à-esquerda) |
| Escopo estático/dinâmico com pseudocódigo Java | [Seção 10](#10-análise-semântica-tabela-de-símbolos-e-escopo) |
| Simulação de análise de precedência de operadores | [Seção 9](#9-análise-por-precedência-de-operadores) |
| Simulação de análise preditiva tabular (pilha) | [Seção 8.3](#83-executando-a-análise-algoritmo-dirigido-por-pilha) |
| Construir a tabela M do zero (questão extra) | [Seções 7](#7-recursão-à-esquerda-e-fatoração-à-esquerda), [6](#6-conjuntos-first-e-follow) e [8.1](#81-construindo-a-tabela-m) |
| Múltipla escolha conceitual (Enade/Poscomp) | [Seções 1](#1-o-que-é-um-compilador), [2](#2-tipos-de-tradutores-de-linguagem), [3](#3-as-fases-internas-de-um-compilador), [12](#12-tópicos-conceituais-enadeposcomp) |

---

## 14. Checklist final

- [x] Sei explicar o que é um compilador e diferenciar dos outros tipos de tradutores.
- [x] Sei listar as fases de um compilador e o que cada uma detecta como erro.
- [x] Sei diferenciar análise top-down de bottom-up sem hesitar.
- [x] Sei escrever um programa SIMPLE com validação de entrada e laço, do zero.
- [x] Sei calcular FIRST e FOLLOW de qualquer gramática pequena em poucos minutos.
- [x] Sei eliminar recursão à esquerda e fazer fatoração à esquerda mecanicamente.
- [x] Sei construir a tabela M completa a partir de uma gramática do zero.
- [ ] Sei simular a execução do analisador preditivo tabular (pilha/entrada/derivação).
- [ ] Sei simular a análise por precedência de operadores (pilha/relação/entrada/ação/handle).
- [ ] Sei montar a pilha de ativação e resolver escopo dinâmico, e sei diferenciar de escopo estático.
- [ ] Revisei os tópicos conceituais estilo Enade/Poscomp.

---

**Bons estudos! 🚀** Se seguir este material na ordem, você cobre 100% dos tipos de questão observados nas provas de 2022, 2023, 2024 e 2025.
