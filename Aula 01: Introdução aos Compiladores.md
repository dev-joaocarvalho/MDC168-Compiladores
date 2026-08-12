# Compiladores — Aula 01: Introdução aos Compiladores

> Material de estudo — resumo teórico + exercícios progressivos + gabarito
> Baseado nos slides da disciplina (CMP01) e no padrão de cobrança das provas anteriores (IESB — Compiladores).

---

## 1. Por que precisamos de tradutores?

- Um programa é escrito em uma **linguagem de alto nível** (próxima da forma como pensamos o problema: Python, C, Java...).
- O computador só entende **linguagem de máquina** (sequências de 0s e 1s) — a chamada **linguagem de baixo nível**.
- É preciso um sistema que faça a ponte entre essas duas linguagens: um **tradutor**.

> **Tradutor** = sistema que recebe um programa escrito em uma **linguagem fonte** e produz um programa equivalente em uma **linguagem objeto**.

### Evolução das linguagens de programação (do mais baixo para o mais alto nível)

1. **Linguagens de máquina** — programas em notação binária.
2. **Linguagens simbólicas (Assembly)** — linguagens de montagem.
3. **Linguagens orientadas ao usuário** — Pascal, Fortran, Algol, Ada, C, C++, Java.
4. **Linguagens orientadas a aplicação** — Excel, SQL, Lotus 1-2-3, Visicalc.
5. **Linguagens de conhecimento** — Prolog.

---

## 2. Tipos de tradutores — não confunda!

| Tradutor | Entrada → Saída | Relação |
|---|---|---|
| **Montador (assembler)** | Assembly → linguagem de máquina | geralmente **1 para 1** |
| **Macro-assembler** | Assembly → linguagem de máquina | **1 para várias** |
| **Compilador** | Alto nível → Assembly ou linguagem de máquina | 1 para várias (bem mais complexo) |
| **Pré-compilador / pré-processador / filtro** | Alto nível estendido → alto nível original | conversão entre **duas linguagens de alto nível** |
| **Interpretador** | Código intermediário + dados → **efeito de execução direto** | não gera código de máquina |

**Ponto-chave que cai em prova:**
O **interpretador não produz um programa objeto** — ele produz o **efeito da execução** do algoritmo original, sem mapeá-lo para linguagem de máquina. Por isso:

- Um programa **compilado** costuma **executar mais rápido** (a tradução já foi feita antes, uma única vez).
- Um programa **interpretado** costuma ser **mais lento na execução** (é traduzido/reinterpretado a cada execução), mas facilita testes e depuração, além de ser mais portável (só é preciso portar o interpretador, não recompilar o programa para cada máquina).

### Compilador — esquema

```
programa fonte ──► [ COMPILADOR ] ──► programa objeto
dados de entrada ──► [ PROGRAMA OBJETO ] ──► resultados
```

### Interpretador — esquema

```
programa fonte ──► [ TRADUTOR ] ──► programa intermediário ──► [ INTERPRETADOR (máquina virtual) ] + dados ──► resultados
```

### Pré-compilador / filtro — esquema

```
Fortran IV Nível G IBM ──► [ FILTRO ] ──► Fortran IV padrão ANSI
```

---

## 3. Estrutura interna de um compilador (o "pipeline")

```
                         programa fonte
                               │
                    ┌──────────▼──────────┐
                    │   Análise Léxica     │──► identifica tokens (palavras-chave, ids, números, operadores)
                    ├──────────────────────┤
                    │   Análise Sintática   │──► verifica se a estrutura gramatical está correta
                    ├──────────────────────┤
   tabelas ◄────────┤   Análise Semântica   │──► verifica se a construção "faz sentido" (tipos, escopo etc.)
   atendimento      ├──────────────────────┤
   a erros ◄────────┤ Geração de Código     │
                    │ Intermediário          │
                    ├──────────────────────┤
                    │ Otimização de Código   │
                    ├──────────────────────┤
                    │ Geração de Código      │
                    │ Objeto                 │
                    └──────────▼──────────┘
                         programa objeto
```

O compilador se divide em duas grandes fases:

- **Análise (front-end):** léxica → sintática → semântica. Aqui o compilador **"entende"** o programa fonte.
- **Síntese (back-end):** código intermediário → otimização → código objeto. Aqui o compilador **"produz"** a saída.

Duas estruturas cruzam **todas** as fases:

- **Tabela de símbolos**: guarda informações sobre declarações de variáveis, procedimentos/subrotinas e seus parâmetros. Algumas tabelas são fixas (palavras reservadas, delimitadores); a mais importante é montada dinamicamente durante a análise do programa fonte.
- **Atendimento a erros**: cada fase pode detectar e reportar erros, mas o tradutor **deve continuar analisando** o restante do texto mesmo após encontrar um erro — não pode parar na primeira ocorrência.

### 3.1 Análise Léxica

Identifica sequências de caracteres que formam **unidades léxicas (tokens)**. Lê o texto fonte caractere a caractere, verifica se pertence ao alfabeto da linguagem, identifica tokens e descarta comentários/espaços desnecessários.

**Exemplo:**
```
while I < 100 do I := J + I;
```
vira a sequência de tokens:
```
[while, ] [id, 7] [<, ] [cte, 13] [do, ] [id, 7] [:=, ] [id, 12] [+, ] [id, 7] [;, ]
```

### 3.2 Análise Sintática

Verifica se a **estrutura gramatical** do programa está correta — se foi formada seguindo as regras gramaticais da linguagem.

### 3.3 Análise Semântica

Verifica se as estruturas do programa **fazem sentido durante a execução** (ex.: compatibilidade de tipos, uso de variáveis não declaradas etc.).

### 3.4 Geração de código intermediário

Utiliza a representação interna produzida pelo analisador sintático e gera uma sequência de código intermediário.

**Vantagens:**
- Possibilita otimizar o código antes de gerar o código objeto final (código mais eficiente).
- Resolve gradualmente a passagem de código fonte (condensado) para código objeto (muitas instruções elementares de baixo nível).

### 3.5 Otimização de código

Otimiza o código intermediário em termos de **velocidade de execução** e **espaço em memória**.

### 3.6 Geração de código objeto

A fase **mais difícil**, pois exige seleção cuidadosa de instruções e registradores da máquina alvo. Objetivos:
- Produção do código objeto.
- Reserva de memória para constantes e variáveis.
- Seleção de registradores.

---

## 4. Estudo de caso: o processador hipotético

Esse exemplo é importante porque conecta a teoria com **cálculo de bits** — tema clássico de prova.

### 4.1 Arquitetura

- Processador de **8 bits** → barramento de dados D0–D7.
- **4 registradores**: R0, R1, R2, R3 → 2 bits bastam para identificar 1 de 4 registradores.
- **16 posições de memória** endereçáveis → barramento de endereços A0–A3 → 4 bits bastam para endereçar 1 de 16 posições.

Registradores ocultos no diagrama (mas parte do processador):
- **Registrador de instruções** — armazena a instrução em execução.
- **Contador de programas** — armazena o endereço da próxima instrução a executar.
- **Registrador de controle** — armazena o estado do resultado da última instrução.

### 4.2 Regra geral para calcular bits

> Para representar **n** opções diferentes, você precisa de **⌈log₂(n)⌉ bits** (arredondando para cima).

Aplicações:
- 4 instruções → log₂(4) = **2 bits** de opcode ✔
- 16 posições de memória → log₂(16) = **4 bits** de endereço ✔
- 4 registradores → log₂(4) = **2 bits** ✔

### 4.3 Instruções (opcode de 2 bits → 4 combinações possíveis)

| Instrução | Opcode | Formato | Exemplo |
|---|---|---|---|
| **LOAD** | 00 | opcode(2) + endereço(4) + registrador(2) | `LOAD 10, R1` → carrega o conteúdo da posição 10 de memória em R1 |
| **STORE** | 01 | opcode(2) + registrador(2) + endereço(4) | `STORE R2, 5` → transfere o conteúdo de R2 para a posição 5 de memória |
| **ADD** | 10 | opcode(2) + reg(2) + reg(2) + reg(2) | `ADD R1, R2, R3` → soma R1+R2, resultado em R3 |
| **BZERO** | 11 | opcode(2) + registrador(2) + endereço(4) | `BZERO R3, 7` → desvia para a posição 7 se R3 = 0 |

### 4.4 Exemplos de codificação binária

```
LOAD 5, R3   → 00 0101 11 → 00010111
              opcode endereço registrador

BZERO R3, 7  → 11 11 0111 → 11110111
              opcode registrador endereço
```

**Exemplo completo — `c = a + b;`** (com `a` na posição 10, `b` na posição 11, `c` na posição 12):

| Linguagem simbólica | Linguagem de máquina |
|---|---|
| `LOAD 10, R1` | `00101001` |
| `LOAD 11, R2` | `00101110` |
| `ADD R1, R2, R0` | `10011000` |
| `STORE R0, 12` | `01001100` |

---

## 5. Resumo rápido (revisão)

- Tradutor = programa que converte linguagem fonte → linguagem objeto.
- Montador: assembly → máquina (1-para-1). Macro-assembler: assembly → máquina (1-para-várias).
- Compilador: alto nível → assembly/máquina.
- Filtro/pré-compilador: alto nível → alto nível.
- Interpretador: **não gera código objeto**, produz o efeito de execução.
- Pipeline do compilador: **léxica → sintática → semântica → código intermediário → otimização → código objeto**, com **tabela de símbolos** e **tratamento de erros** atravessando tudo.
- Bits necessários para representar *n* opções: **⌈log₂(n)⌉**.

---

## 6. Exercícios propostos

**Nível 1 — Conceitos**

1. Explique com suas palavras o que são: compilador, interpretador, montador e pré-compilador.
2. Qual a diferença entre um compilador e um interpretador?

**Nível 2 — Comparação / raciocínio**

3. Aponte vantagens e desvantagens dos interpretadores em relação aos compiladores.
4. No processador hipotético, por que os projetistas mantiveram as instruções `LOAD` e `STORE` separadas, em vez de uma única instrução `MOVE` (onde a ordem dos operandos indicaria a direção da transferência)?

**Nível 3 — Cálculo de bits**

5. Quantos bits são necessários para representar instruções de um processador com **40 instruções**, cada uma podendo referenciar **um único endereço de memória com 64 posições**? Assuma que todos os opcodes têm o mesmo número de bits.

**Nível 4 — Codificação binária** (use o formato do processador hipotético da aula: opcode 2 bits, registrador 2 bits, endereço 4 bits)

6. Qual o código binário das instruções:
   a) `LOAD 3, R2`
   b) `STORE R1, 9`
   c) `ADD R0, R1, R3`
   d) `BZERO R2, 12`

**Nível 5 — Síntese / aplicação (estilo ENADE / provas anteriores)**

7. Explique o processo de compilação completo: cite todas as fases e como elas se relacionam entre si (inclua a tabela de símbolos e o tratamento de erros).
8. Se a segunda geração do processador hipotético **dobrasse o número de registradores** (de 4 para 8) **e dobrasse a capacidade de endereçamento de memória** (de 16 para 32 posições), qual seria o novo formato binário (em bits) da instrução `LOAD`? Quantos bits passaria a ter a instrução no total?

---

## 7. Gabarito comentado

**1.**
- **Compilador**: tradutor que mapeia um programa escrito em linguagem de alto nível para um programa equivalente em linguagem simbólica ou linguagem de máquina.
- **Interpretador**: tradutor que aceita como entrada o código intermediário de um programa e produz o **efeito de execução** do algoritmo original, sem gerar código de máquina.
- **Montador (assembler)**: tradutor que mapeia instruções em linguagem simbólica (assembly) para instruções de linguagem de máquina, geralmente numa relação de um-para-um.
- **Pré-compilador (filtro)**: tradutor que converte instruções escritas numa linguagem de alto nível estendida para instruções da linguagem original — ou seja, converte entre duas linguagens de alto nível.

**2.**
O compilador **gera um programa objeto** separado, que pode ser executado depois, de forma independente do compilador. O interpretador **não gera um programa objeto**: ele mesmo executa cada instrução do código intermediário e produz o resultado diretamente, precisando estar presente durante toda a execução do programa.

**3.**
- *Vantagens do interpretador*: mais fácil de depurar (o erro é identificado durante a própria execução, com o contexto de estado do programa); execução pode começar imediatamente, sem uma fase de compilação separada; maior portabilidade (basta portar o interpretador para uma nova máquina, sem recompilar cada programa).
- *Desvantagens*: execução mais lenta (a tradução/interpretação é refeita a cada execução, inclusive dentro de laços); o interpretador precisa estar sempre presente/disponível; em geral, maior consumo de memória durante a execução.

**4.**
Manter `LOAD` e `STORE` como instruções separadas torna o **opcode autoexplicativo**: o formato e o sentido da transferência (memória→registrador ou registrador→memória) já ficam definidos só pelo código de operação, sem exigir que o hardware de controle infira a direção pela ordem dos operandos. Isso simplifica a decodificação da instrução e reduz a chance de ambiguidade/erro na interpretação do formato binário.

**5.**
- Opcode: 40 instruções → ⌈log₂(40)⌉ = **6 bits** (2⁵=32 < 40 ≤ 64=2⁶)
- Endereço: 64 posições → ⌈log₂(64)⌉ = **6 bits**
- **Total: 6 + 6 = 12 bits** por instrução.

**6.** (opcode 2 bits | LOAD/BZERO: endereço(4)+registrador(2) | STORE: registrador(2)+endereço(4) | ADD: reg(2)+reg(2)+reg(2); registradores: R0=00, R1=01, R2=10, R3=11)

a) `LOAD 3, R2` → opcode `00` + endereço `0011` + reg `10` → **`00001110`**
b) `STORE R1, 9` → opcode `01` + reg `01` + endereço `1001` → **`01011001`**
c) `ADD R0, R1, R3` → opcode `10` + `00` + `01` + `11` → **`10000111`**
d) `BZERO R2, 12` → opcode `11` + reg `10` + endereço `1100` → **`11101100`**

**7.**
O processo de compilação se divide em duas grandes fases:

- **Análise** (entender o programa fonte):
  1. *Léxica* — identifica tokens (palavras-chave, identificadores, constantes, operadores), descartando espaços e comentários.
  2. *Sintática* — verifica se a sequência de tokens obedece à gramática da linguagem, montando uma representação estrutural (ex.: árvore sintática).
  3. *Semântica* — verifica se as construções fazem sentido (tipos compatíveis, variáveis declaradas, escopo correto).
- **Síntese** (produzir o programa objeto):
  4. *Geração de código intermediário* — traduz a representação sintática/semântica para uma sequência de código intermediário, independente de máquina.
  5. *Otimização de código* — melhora o código intermediário em velocidade de execução e uso de memória.
  6. *Geração de código objeto* — seleciona instruções e registradores da máquina alvo, produzindo o código final.

Duas estruturas **atravessam todas as fases**: a **tabela de símbolos**, que registra e é consultada com informações sobre variáveis, procedimentos e parâmetros; e o **atendimento a erros**, que trata os problemas detectados em qualquer fase sem interromper a análise do restante do programa.

**8.**
- Registradores: 8 → ⌈log₂(8)⌉ = **3 bits** (antes eram 2 bits para 4 registradores)
- Endereçamento: 32 posições → ⌈log₂(32)⌉ = **5 bits** (antes eram 4 bits para 16 posições)
- Opcode permanece **2 bits** (não foi alterado nesta questão)
- Novo formato de `LOAD`: opcode(2) + endereço(5) + registrador(3)
- **Total: 2 + 5 + 3 = 10 bits** (antes eram 8 bits)

---

*Material de apoio gerado a partir dos slides da disciplina (CMP01 — Introdução à Compiladores) e do padrão de exercícios/provas do curso. Bons estudos!*
