# 📗 Gabarito Completo — Todas as Provas Antigas (Compiladores IESB/Ybadoo)

> Este arquivo resolve **todas as questões** das 4 provas disponibilizadas (1º/2022, 2º/2023, 2º/2024, 2º/2025), explicando o método usado em cada uma e como resolver se o professor mudar os dados. Onde a questão é de uma prova pública (Enade/Poscomp), o gabarito foi **conferido na fonte oficial**. Onde é uma questão específica do Ybadoo (sem gabarito público divulgado), o resultado foi **recalculado do zero, célula por célula**, e o raciocínio completo é mostrado para você conferir.
>
> ⚠️ **Aviso de transparência:** as questões do Ybadoo (assertivas I/II/III/IV sobre FIRST/FOLLOW, recursão/fatoração e escopo) não têm gabarito oficial publicado. As respostas abaixo são o resultado do meu próprio cálculo, mostrado passo a passo — confira com seu professor/monitor se tiver dúvida em alguma. As questões de Enade/Poscomp foram verificadas contra a fonte oficial.

---

## Índice
- [Prova 1º Semestre 2022](#prova-1º-semestre-2022)
- [Prova 2º Semestre 2023](#prova-2º-semestre-2023)
- [Prova 2º Semestre 2024](#prova-2º-semestre-2024)
- [Prova 2º Semestre 2025](#prova-2º-semestre-2025)
- [Tabela-resumo de todos os gabaritos](#tabela-resumo-de-todos-os-gabaritos)

---

## Prova 1º Semestre 2022

### Questão 01 — Programa SIMPLE (MDC pelo Algoritmo de Euclides)

**Resposta oficial (dada no gabarito da prova):**
```
10 input a
15 if a <= 0 goto 55
20 input b
25 if b <= 0 goto 55
30 if b == 0 goto 60
35 let r = a % b
40 let a = b
45 let b = r
50 goto 30
55 let a = -1
60 print a
65 end
```

**Método (funciona para qualquer MDC pedido de forma diferente):**
1. Valide as duas entradas logo após cada `input`.
2. O Algoritmo de Euclides: enquanto `b ≠ 0`, faça `r = a % b`, depois `a = b`, `b = r`. Quando `b == 0`, o MDC é o valor de `a`.
3. Trate isso como um laço clássico: teste `b == 0` no topo (se for, pula pro final e imprime `a`); senão, executa o corpo e volta pro teste.
4. **Se o professor pedir o MMC** (como na prova de 2023): lembre que `MMC(a,b) = (a * b) / MDC(a,b)`. Calcule o MDC com o mesmo laço de Euclides, guardando o produto `p = a*b` **antes** de a e b mudarem, e no final divida `p` pelo MDC encontrado.

---

### Questão 02 — FIRST e FOLLOW

**Gramática:**
```
S → AC | CdB | Ba
A → aA | BC
B → bB | CB | ε
C → cC | ε
```

**Cálculo completo:**

FIRST:
- `FIRST(C) = {c, ε}`
- `FIRST(B) = {b, c, ε}` (de `bB`, de `CB` herdando FIRST(C), e do próprio ε)
- `FIRST(A) = {a, b, c, ε}` (de `aA`; de `BC` herdando FIRST(B), e como ε∈FIRST(B), herdando também FIRST(C))
- `FIRST(S) = {a, b, c, d, ε}` — atenção aqui: S→AC pode gerar ε inteiro, porque A pode virar ε (via `BC` com B e C ambos ε) **e** o C que sobra também pode virar ε. Por isso ε entra em FIRST(S).

FOLLOW:
- `FOLLOW(S) = {$}`
- `FOLLOW(A) = {c, $}` (de S→AC, FIRST(C)−{ε}={c}, e como ε∈FIRST(C), herda FOLLOW(S)={$})
- `FOLLOW(B) = {a, c, $}` (de S→CdB herdando FOLLOW(S); de S→Ba com FIRST(a)={a}; de A→BC com FIRST(C)−{ε}={c} e herdando FOLLOW(A))
- `FOLLOW(C) = {a, b, c, d, $}` (de S→AC herdando FOLLOW(S); de S→CdB com 'd'; de A→BC herdando FOLLOW(A)={c,$}; de B→CB com FIRST(B)−{ε}={b,c} e herdando FOLLOW(B)={a,c,$})

**Conferindo as assertivas:**
- **I.** "FIRST(S) é {a,b,c,d,ε} e FOLLOW(C) é {a,b,c,d,$}" → **ambos batem exatamente** → ✅ CORRETA
- **II.** "FIRST(A) é {a,b,c} e FOLLOW(B) é {a,c,$}" → FIRST(A) real tem ε também (falta), FOLLOW(B) bate → ❌ INCORRETA (por causa do FIRST(A) incompleto)
- **III.** "FIRST(B) é {b,c,ε} e FOLLOW(A) é {c,$}" → ambos batem exatamente → ✅ CORRETA
- **IV.** "FIRST(A) é {a,b,c,ε} e FOLLOW(C) é {a,c,d,$}" → FIRST(A) bate, mas FOLLOW(C) real tem o 'b' também (falta) → ❌ INCORRETA

**Resposta: b) apenas as assertivas I e III.**

**Se a gramática mudar:** refaça o cálculo do zero seguindo as 3 regras de FIRST e 3 regras de FOLLOW (ver seção 6 do guia principal). Nunca tente "reconhecer" a resposta — sempre recalcule.

---

### Questão 03 — Recursão à esquerda e fatoração à esquerda

**Assertiva I:** `B → xAB | CA | xB` fatorada vira `B → xB₁ | CA ; B₁ → AB | B`
→ Prefixo comum entre `xAB` e `xB` é só `x` (o segundo símbolo, A vs B, já diverge). Removendo `x`: sobra `AB` e `B`. **Confere exatamente.** ✅ CORRETA

**Assertiva II:** `C → xy | xDB` fatorada vira `C → xC₁ ; C₁ → y | DB | ε`
→ Prefixo comum é `x`. Removendo: sobra `y` e `DB` — **nenhuma das duas sobras é vazia**, então **não deveria ter ε** nessa produção. Colocar ε aqui **muda a linguagem** (passaria a aceitar só "x" sozinho, o que a gramática original nunca gerava). ❌ INCORRETA

**Assertiva III:** `B → a | BC | DC` com eliminação de recursão dando `B → a | DC | aB₁ | DCB₁ ; B₁ → C | CB₁`
→ Aqui β={a, DC} e α={C}. A forma "canônica" seria `B → aB₁ | DCB₁ ; B₁ → CB₁ | ε`. A forma da assertiva é uma **variação equivalente**: mantém as alternativas diretas (a, DC) para cobrir o caso de "zero repetições de C", e usa B₁ (sem ε, exigindo pelo menos 1 C) para cobrir "uma ou mais repetições". Testando as duas formas, elas geram exatamente a mesma linguagem. ✅ CORRETA (forma alternativa válida)

**Assertiva IV:** `A → BA | BAA | b | bA`, `B → b | AA | a` — resultado dado mantém A **idêntico ao original** (só reordenado) e o resultado de B ainda contém alternativas começando com o próprio B (`BAA`, `BAAA`), ou seja, **a recursão não foi realmente eliminada** — o resultado ainda tem left recursion residual, o que é uma contradição (o enunciado diz que eliminou, mas o resultado mostrado prova que não eliminou). ❌ INCORRETA

**Resposta: b) apenas as assertivas I e III.**

**Se a gramática mudar:** aplique sempre a fórmula canônica (β vira `βA'`, α vira `A' → αA' | ε`) — é a forma mais segura de acertar. Formas alternativas podem ser válidas, mas arriscam divergir do que o professor espera.

---

### Questão 04 — Escopo dinâmico

**Tabela de variáveis:**
| Função | Variáveis locais |
|---|---|
| sub1 | a, y, z, w |
| sub2 | a, b, z, w |
| sub3 | a, b, c, w |
| sub4 | a, b, c, d |
| main | x, y, z, w |

**⚠️ Correção importante:** quando expliquei escopo dinâmico nesta conversa pela primeira vez, usei esse mesmo exemplo (`main→sub3→sub4→sub2`) e escrevi o resultado como "x de main" — **isso estava incompleto, faltava o `y`**. Meu erro foi esquecer de checar se `y` (declarado em main) era sombreado por alguém na pilha — e não era (nenhuma outra função nessa sequência específica declara `y`). Corrigindo abaixo com o cálculo completo:

**I.** `main → sub3 → sub4 → sub2` (sub2 no topo)
Pilha: main(x,y,z,w) → sub3(a,b,c,w) → sub4(a,b,c,d) → sub2(a,b,z,w)
- sub2: a,b,z,w (visíveis, locais)
- sub4: a,b sombreados por sub2; c,d novos → visíveis
- sub3: a,b,w sombreados; c sombreado por sub4 → nada novo
- main: **x e y não são sombreados por ninguém** → visíveis; z,w sombreados por sub2
→ Resultado: **x, y (main); a, b, z, w (sub2); c, d (sub4)** → ✅ bate exatamente com a assertiva I → CORRETA

**II.** `main → sub4 → sub1 → sub3` (sub3 no topo)
Pilha: main(x,y,z,w) → sub4(a,b,c,d) → sub1(a,y,z,w) → sub3(a,b,c,w)
- sub3: a,b,c,w (visíveis)
- sub1: a sombreado(sub3); w sombreado(sub3); y,z novos → visíveis
- sub4: a,b,c sombreados(sub3); **d novo → visível** (a assertiva OMITE o d!)
- main: x novo → visível; y sombreado(sub1); z sombreado(sub1); w sombreado(sub3)
→ Resultado correto: **x (main); y, z (sub1); d (sub4); a, b, c, w (sub3)**. A assertiva II lista "x de main, y e z de sub1 e a,b,c,w de sub3" e **esquece o "d de sub4"** → ❌ INCORRETA

**III.** `main → sub2 → sub1 → sub4` (sub4 no topo)
Pilha: main(x,y,z,w) → sub2(a,b,z,w) → sub1(a,y,z,w) → sub4(a,b,c,d)
- sub4: a,b,c,d (visíveis)
- sub1: a sombreado(sub4); **y,z,w novos → visíveis** (sub4 não tem y,z,w)
- sub2: a,b sombreados(sub4); z,w sombreados **por sub1** (sub1 está mais perto do topo que sub2!) → nada novo
- main: x novo → visível; y,z,w sombreados(sub1)
→ Resultado correto: **x (main); y, z, w (sub1); a, b, c, d (sub4)**. A assertiva III atribui o `w` a sub2, mas na verdade é `sub1` que "ganha" o w (está mais perto do topo) → ❌ INCORRETA

**IV.** `main → sub1 → sub4 → sub2` (sub2 no topo)
Pilha: main(x,y,z,w) → sub1(a,y,z,w) → sub4(a,b,c,d) → sub2(a,b,z,w)
- sub2: a,b,z,w (visíveis)
- sub4: a,b sombreados(sub2); c,d novos → visíveis
- sub1: a sombreado(sub2); z,w sombreados(sub2); **y novo → visível**
- main: x novo → visível; y sombreado(sub1); z,w sombreados(sub2)
→ Resultado: **x (main); y (sub1); a, b, z, w (sub2); c, d (sub4)** → ✅ bate exatamente → CORRETA

**Resposta: c) apenas as assertivas I e IV.**

**Método geral (funciona sempre):** monte uma tabela com uma coluna por função, na ordem exata da pilha (da mais antiga/embaixo à mais recente/topo). Para cada nome de variável, marque-o **só na função mais à direita (mais recente)** que o declara. Cuidado especial: se uma função "do meio" da pilha declara uma variável, ela só aparece se **nenhuma função mais próxima do topo** também a declarar — mesmo que a função "dona" originalmente pensada não seja a mais óbvia.

---

### Questão 05 — Precedência de operadores

A prova já traz a simulação **completa e correta**, célula por célula, para a entrada `a (b ⊕ d) e`. O método está detalhado na seção 9 do guia principal — é só aplicar `<` (empilha), `>` (reduz), `=` (combina parênteses) conforme a tabela dada, tratando os erros exatamente como o enunciado define (erro 1, erro 2 etc.).

**Se a entrada mudar:** siga o mesmo processo, símbolo a símbolo, sempre comparando o topo da pilha (ignorando não-terminais) com a entrada atual.

---

### Questão 06 — Análise preditiva tabular

A prova já traz a simulação **completa** para `x y z + x z / * - y +`. Mesmo processo: pilha × entrada × tabela M, sempre no formato **Pilha | Entrada | Derivação**.

---

### Questão Extra — Construir a tabela M do zero

A prova já traz FIRST/FOLLOW e a tabela M completas e corretas. Destaque: repare que `M[C,$] = C→ε` e `M[F,x]/[F,y]/[F,z]` têm entradas próprias além do `F→ε` em `$` — isso vem exatamente da regra "se ε∈FIRST(X), preencha também as células do FOLLOW(X)".

---

## Prova 2º Semestre 2023

### Questão 01 — Programa SIMPLE (MMC)

**Resposta oficial:**
```
10 input a
15 if a <= 0 goto 65
20 input b
25 if b <= 0 goto 65
30 let p = a * b
35 let r = a % b
40 let a = b
45 let b = r
50 if r != 0 goto 35
55 let x = p / a
60 goto 70
65 let x = -1
70 print x
75 end
```
**Método:** MMC(a,b) = (a×b)/MDC(a,b). Guarda `p = a*b` **antes** do laço de Euclides começar a modificar `a` e `b`, roda o laço normalmente, e no final divide `p` pelo MDC (que sobra em `a` quando `r` chega a zero).

---

### Questão 02 — FIRST e FOLLOW

**Gramática (repare: aqui B NÃO tem produção ε! Isso muda tudo):**
```
S → AC | CdB | Ba
A → aA | BC
B → bB | CB
C → cC | ε
```

**Cálculo completo (atenção à diferença-chave):**
- `FIRST(C) = {c, ε}`
- `FIRST(B) = {b, c}` — **sem ε**, porque nenhuma das produções de B (`bB` ou `CB`) consegue "zerar" para a cadeia vazia (a regra que adicionaria ε a B depende de B já ter ε no seu próprio FIRST, o que nunca acontece — é um ponto fixo que nunca "liga")
- `FIRST(A) = {a, b, c}` — **sem ε** (porque `BC` não tem ε em FIRST(B), então nem olha o C)
- `FIRST(S) = {a, b, c, d}` — **sem ε** (porque agora `S → AC` não consegue mais zerar via A)

FOLLOW:
- `FOLLOW(A) = {c, $}`
- `FOLLOW(B) = {a, c, $}`
- `FOLLOW(C) = {b, c, d, $}` — **repare que aqui NÃO tem 'a'!** Isso porque, em `B → CB`, como agora FIRST(B) não tem ε, a regra "herdar FOLLOW(B)" não se aplica — só entra `FIRST(B)−{ε} = {b,c}` em FOLLOW(C), sem trazer o FOLLOW(B) (que tinha o 'a').

**Conferindo as assertivas:**
- **I.** "FIRST(B) é {b,c} e FOLLOW(C) é {a,b,c,d,$}" → FIRST(B) bate, mas FOLLOW(C) real é {b,c,d,$} (sem o 'a') → ❌ INCORRETA
- **II.** "FIRST(A) é {a,b,c,ε} e FOLLOW(B) é {a,c,$}" → FIRST(A) real não tem ε → ❌ INCORRETA
- **III.** "FIRST(S) é {a,b,c,d} e FOLLOW(B) é {a,c,$}" → ambos batem exatamente → ✅ CORRETA
- **IV.** "FIRST(A) é {a,b,c} e FOLLOW(C) é {b,c,d,$}" → ambos batem exatamente → ✅ CORRETA

**Resposta: e) apenas as assertivas III e IV.**

**💡 A lição mais importante desta questão:** uma única produção ε a mais ou a menos (aqui, tirar o `B → ε` que existia em 2022) muda em cascata o FIRST e o FOLLOW de **várias** outras variáveis. Sempre recalcule do zero, nunca reaproveite um cálculo de uma gramática "parecida".

---

### Questão 03 — Recursão à esquerda e fatoração à esquerda

**Assertiva I:** `B → xAB | CA | xB` fatorada em `B → xB₁ | CA ; B₁ → AB₂ ; B₂ → B | ε`
→ A fatoração correta pára em `B₁ → AB | B` (como na prova de 2022). Aqui, ao criar um segundo nível (`B₂ → B|ε`), o `B₁ → AB₂` passa a permitir que B₁ derive **apenas "A" sozinho** (quando B₂→ε) — isso **não existia** na gramática original (`AB` sempre exigia o B depois). Muda a linguagem. ❌ INCORRETA

**Assertiva II:** `D → yzA | y | yz` fatorada em `D → yD₁ ; D₁ → zD₂ | ε ; D₂ → A | ε`
→ Prefixo comum "y" entre as 3 alternativas. Removendo: sobra `zA`, `ε` (nada), `z`. Entre essas, `zA` e `z` compartilham novo prefixo "z" — fatorando de novo: sobra `A` e `ε`. Resultado: exatamente como a assertiva descreve. ✅ CORRETA

**Assertiva III:** eliminação de recursão indireta entre B e D (`B → a|aA|DC|DCA`, `D → c|BD`) — resultado mantém B inalterado (correto, pois B não referencia D em posição inicial após a substituição) e D ganha a forma expandida com β diretos + βD₁, sem ε explícito em D₁ (forma alternativa equivalente, igual à assertiva III de 2022). Testando a linguagem gerada, bate com a forma canônica. ✅ CORRETA

**Assertiva IV:** `D → c | DB` — resultado mostra `D₁ → B | D₁ | BD₁`, que contém a regra sem sentido "D₁ → D₁" (uma produção apontando pra si mesma sem consumir nada) — isso é claramente um erro de formulação, não uma eliminação de recursão válida. ❌ INCORRETA

**Resposta: c) apenas as assertivas II e III.**

---

### Questão 04 — Escopo dinâmico

**Mesma tabela de variáveis da prova de 2022** (sub1 a sub4 e main têm exatamente as mesmas variáveis).

**I.** `main → sub4 → sub2 → sub1` (sub1 no topo)
Pilha: main(x,y,z,w) → sub4(a,b,c,d) → sub2(a,b,z,w) → sub1(a,y,z,w)
- sub1: a,y,z,w (visíveis)
- sub2: a,z,w sombreados(sub1); **b novo → visível**
- sub4: a sombreado(sub1); **b sombreado por sub2** (não por sub4!); c,d novos → visíveis
- main: x novo → visível; y,z,w sombreados(sub1)
→ Resultado correto: **x (main); a,y,z,w (sub1); b (sub2); c,d (sub4)**. A assertiva I atribui o `b` a sub4, mas na verdade é sub2 quem "ganha" o b (está mais perto do topo que sub4) → ❌ INCORRETA

**II.** `main → sub3 → sub2 → sub4` (sub4 no topo)
Pilha: main(x,y,z,w) → sub3(a,b,c,w) → sub2(a,b,z,w) → sub4(a,b,c,d)
- sub4: a,b,c,d (visíveis)
- sub2: a,b sombreados(sub4); z,w novos → visíveis
- sub3: a,b,c sombreados(sub4); w sombreado(sub2) → nada novo
- main: x,y novos → visíveis; z,w sombreados(sub2)
→ Resultado: **x,y (main); z,w (sub2); a,b,c,d (sub4)** → ✅ bate exatamente → CORRETA

**III.** `main → sub2 → sub4 → sub1` (sub1 no topo)
Pilha: main(x,y,z,w) → sub2(a,b,z,w) → sub4(a,b,c,d) → sub1(a,y,z,w)
- sub1: a,y,z,w (visíveis)
- sub4: a sombreado(sub1); **b,c,d novos → visíveis**
- sub2: a,b sombreados; z,w sombreados(sub1) → nada novo
- main: x novo → visível; y,z,w sombreados(sub1)
→ Resultado correto: **x (main); a,y,z,w (sub1); b,c,d (sub4)**. A assertiva III atribui o `b` a sub2, mas é sub4 quem ganha (mais perto do topo) → ❌ INCORRETA

**IV.** `main → sub1 → sub3 → sub2` (sub2 no topo)
Pilha: main(x,y,z,w) → sub1(a,y,z,w) → sub3(a,b,c,w) → sub2(a,b,z,w)
- sub2: a,b,z,w (visíveis)
- sub3: a,b,w sombreados(sub2); **c novo → visível**
- sub1: a sombreado(sub2); z,w sombreados(sub2); **y novo → visível**
- main: x novo → visível; y sombreado(sub1); z,w sombreados(sub2)
→ Resultado: **x (main); y (sub1); a,b,z,w (sub2); c (sub3)** → ✅ bate exatamente → CORRETA

**Resposta: d) apenas as assertivas II e IV.**

---

### Questões 05, 06 e Extra

Já vêm com a simulação completa e correta na prova — aplique o mesmo processo explicado na Q05/Q06/Extra de 2022. Únicas diferenças: entrada distinta (`a ∨ (b d) ⊕ e`) e gramática distinta na Extra (troque `A→zB` por `A→xB`... releia a tabela M final dada, ela já está pronta e correta).

---

## Prova 2º Semestre 2024

### Questão 01 — Programa SIMPLE (Sequência de Tribonacci)

**Resposta oficial:**
```
10 input n
15 if n < 0 goto 75
20 let a = 0
25 let b = 1
30 let c = 1
35 print a
40 if n < 1 goto 85
45 let x = a + b
50 let a = b
55 let b = c
60 let c = c + x
65 let n = n - 1
70 goto 35
75 let a = -1
80 print a
85 end
```
**Método:** sequências recorrentes (Fibonacci, Tribonacci) sempre se resolvem guardando os "últimos termos" em variáveis (aqui `a,b,c` guardam os 3 últimos), imprimindo o termo atual, calculando o próximo (`x = a+b`, embora o nome sugira Fibonacci, aqui a lógica usa 3 termos porque Tribonacci soma os 3 anteriores), "deslizando" as variáveis (`a=b, b=c, c=c+x`) e decrementando o contador `n` até zerar.

**Se pedir Fibonacci puro (2 termos) em vez de Tribonacci:** simplifique para guardar só `a` e `b`, com `x = a+b`, depois `a=b`, `b=x`.

---

### Questão 02 — Precedência de operadores

Simulação completa já dada na prova para `a + b * c * d + e`. Segue o mesmo método da seção 9 do guia principal.

---

### Questão 03 — Poscomp 2022 (Reconhecedor de linguagem)

**Pergunta:** dada `S ::= (S) S | ε`, qual é o reconhecedor?

**Resposta oficial (confirmada no comentário da própria prova): d) Autômato de Pilha.**

**Por quê:** pela Hierarquia de Chomsky, gramáticas livres de contexto são reconhecidas por Autômatos de Pilha (a "memória extra" da pilha é necessária para "contar" os parênteses aninhados corretamente). Expressões regulares **geram**, não reconhecem. Autômatos finitos só reconhecem linguagens regulares (sem aninhamento).

---

### Questão 04 — Assertivas conceituais sobre análise sintática

- **I.** "Bottom-up percorre a árvore das folhas até a raiz" → **verdadeiro**, é a definição correta de análise ascendente.
- **II.** "Top-down percorre a árvore das folhas (terminais... não, ele erra: diz que folhas representam variáveis) até a raiz (que representa terminais)" → **falso em dois pontos**: top-down vai da raiz às folhas (não o contrário), e folhas representam terminais (não variáveis) — a assertiva inverteu tudo.
- **III.** "Conflitos em tabelas de precedência: (a) falta de relação entre topo da pilha e entrada; (b) handle suposto sem produção correspondente" → **verdadeiro**, bate exatamente com a teoria (e com os "erro 1" e "erro de reduzir handle" vistos nas próprias provas).
- **IV.** "Uma gramática com X→abBc e Y→ab não pode ser processada por análise preditiva, pois o prefixo 'ab' aparece em ambas" → **falso**: fatoração à esquerda só importa quando duas produções do **mesmo** não-terminal compartilham prefixo. X e Y são não-terminais diferentes — não há conflito real, pois o analisador já sabe qual não-terminal está processando (está no topo da pilha).

**Resposta: b) apenas as assertivas I e III.** (confirmado pelos próprios comentários da prova, que reforçam exatamente esses dois pontos)

---

### Questão 05 — Poscomp 2023 (Tokens por expressão regular)

**Tokens:** T1 = k?b?zz*k ; T2 = z?k?bb*z ; T3 = b?z?kk*b — sempre casando o **maior prefixo possível**.

**Entrada:** `kkbzkbbkkb`

Simulação (maior prefixo em cada passo):
1. Restando `kkbzkbbkkb`: T3 casa `kkb` (maior entre as 3 opções) → token T3
2. Restando `zkbbkkb`: T1 casa `zk` (2 chars), T3 casa `zkb` (3 chars, maior) → token T3
3. Restando `bkkb`: T1 e T2 falham, T3 casa `bkkb` (tudo) → token T3

**Resultado: T3 T3 T3 → Resposta: e) T3 T3 T3.**

**Método geral:** sempre teste as 3 expressões regulares contra o que resta da entrada, escolha a que casa **mais caracteres**, "corte" esse pedaço, repita até acabar a entrada.

---

### Questão 06 — Escopo estático (programa em C)

```c
int a = 10;
void xpto() { printf("%d ", a); }
int main() {
  { int a = 20;
    { int a = 30;
      xpto();
      printf("%d ", a);
    }
    printf("%d ", a);
  }
  printf("%d ", a);
}
```

**Ponto-chave:** `xpto()` está definida **fora** de qualquer bloco de `main` (no nível do arquivo/global). Em escopo estático, a variável que `xpto()` enxerga é decidida por **onde ela está escrita no texto**, não por quem a chamou. Como `xpto()` está no nível global, ela só enxerga o `a` **global** (=10) — mesmo sendo chamada de dentro de blocos com `a` locais (20, 30).

**Trace:**
1. Entra no bloco externo: `a=20` local
2. Entra no bloco interno: `a=30` local
3. Chama `xpto()` → imprime o **global** `a=10` → `"10 "`
4. `printf` dentro do bloco interno → imprime `a=30` (o local mais próximo) → `"30 "`
5. Sai do bloco interno, `printf` no bloco externo → imprime `a=20` → `"20 "`
6. Sai do bloco externo, `printf` final → imprime `a=10` (global) → `"10 "`

**Saída: "10 30 20 10" → Resposta: a) 10 30 20 10.**

---

### Questão Extra — Construir a tabela M do zero

A prova já traz FIRST/FOLLOW e tabela M completos e corretos (grammar com A→a|(B), B→aD|(B)D, C→;AD, D→C|ε).

---

## Prova 2º Semestre 2025

### Questão 01 — Enade 2017 (Conflito na tabela LL(1))

**Resposta oficial (confirmada na fonte oficial do Enade): b) de um não-determinismo causado por uma ambiguidade na gramática.**

**Explicação (refazendo o cálculo):** gramática `X→aZbXY|c`, `Y→dX|ε`, `Z→e`.
- `FOLLOW(X)` acaba incluindo `d` (porque, dentro de `X→aZbXY`, o X do meio é seguido por Y, e Y pode começar com `d`).
- `FOLLOW(Y)` herda `FOLLOW(X)`, então também tem `d`.
- Na tabela M: `M[Y,d]` recebe `Y→dX` (direto, pois `d∈FIRST(Y)`) **e também** `Y→ε` (porque `ε∈FIRST(Y)` e `d∈FOLLOW(Y)`).
- Duas produções na mesma célula = a gramática permite duas formas diferentes de interpretar a mesma entrada em certos pontos → **ambiguidade estrutural real**, não um erro de construção da tabela.

---

### Questão 02 — Enade 2023 (Análise semântica e divisão por zero)

**Sentença:** `a = a / (b - b);` — testada contra a gramática `S→T=E;|T=E;S`, `E→E+E|E-E|E*E|E/E|(E)|T`, `T→a|b`.

**Resposta: b) a gramática gera a sentença apresentada e o erro presente na expressão está fora do escopo das análises sintática e semântica.**

**Por quê:** a sentença é perfeitamente válida pela gramática (é uma atribuição de uma divisão de duas expressões, tudo encaixando nas regras). O "erro" aqui é a divisão por zero (`b - b` sempre vale 0) — mas isso é um **erro de tempo de execução**, não algo que a análise sintática (estrutura) ou semântica clássica (tipos, declaração de variáveis) verifica. Análise semântica tradicional não avalia o *valor* das expressões em tempo de compilação.

---

### Questão 03 — Programa SIMPLE (Número Perfeito)

Como o enunciado não trouxe solução pronta, aqui está uma resolvida e testada (n=6 → 1; n=8 → 0; n=1 → -1):

```
10 input n
15 if n < 2 goto 90
20 let s = 0
25 let i = 1
30 if i >= n goto 60
35 let r = n % i
40 if r != 0 goto 50
45 let s = s + i
50 let i = i + 1
51 goto 30
60 if s == n goto 80
65 let x = 0
70 goto 95
80 let x = 1
85 goto 95
90 let x = -1
95 print x
99 end
```

**Método:** percorra `i` de 1 até `n-1` (divisores próprios possíveis); toda vez que `n % i == 0`, some `i` numa variável `s`; no final, compare `s` com `n`. Se forem iguais, é número perfeito.

**Teste manual (n=6):** i=1→r=0,s=1; i=2→r=0,s=3; i=3→r=0,s=6; i=4,5→r≠0. Laço termina (i=6≥n=6). s=6=n → x=1. ✅

---

### Questão 04 — Enade 2021 (Assertivas conceituais)

- **I.** "O analisador sintático verifica se a sequência de tokens compõe um programa válido" → **verdadeiro** (é exatamente a função da análise sintática).
- **II.** "Na análise léxica, a mesma classificação vale para Java, Pascal ou qualquer linguagem" → **falso** — cada linguagem define seus próprios tokens/palavras-reservadas; a classificação é específica da linguagem.
- **III.** "O analisador semântico verifica incoerências de significado das construções" → **verdadeiro**, descrição correta e compatível com o que vimos na análise semântica.
- **IV.** "O analisador sintático aponta erros de divergência de tipo entre operadores e operandos" → **falso** — isso é trabalho da análise **semântica**, não da sintática (a sintática só olha estrutura, não tipos).

**Resposta: b) nas assertivas I e III.**

---

### Questão 05 — Poscomp 2024 (Esquema de Tradução)

**Resposta: e) Gramática livre de contexto na qual fragmentos de programas (ações) são inseridos nos lados direitos das regras de produção.**

Essa é a definição padrão e consolidada de Esquema de Tradução (Aho/Ullman) — junta a estrutura gramatical com ações semânticas embutidas, usada para gerar código ou fazer verificações durante o parsing.

---

### Questão 06 — Precedência de operadores

**Entrada:** `a * b (c) d` — repare que faltam operadores entre `b` e `(`, e entre `)` e `d`. Isso é proposital: a tabela trata isso com "erro 2" (insere `+` automaticamente e segue a análise).

Simulação completa (usando "A" como rótulo genérico para qualquer não-terminal reduzido, seguindo a convenção das provas):

| Pilha | Relação | Entrada | Ação |
|---|---|---|---|
| $ | < | a * b ( c ) d $ | empilha a |
| $a | > | * b ( c ) d $ | reduz D→a |
| $A | < | * b ( c ) d $ | empilha * |
| $A* | < | b ( c ) d $ | empilha b |
| $A*b | erro2 | ( c ) d $ | insere + (operador esperado) |
| $A*b | > | + ( c ) d $ | reduz D→b |
| $A*A | > | + ( c ) d $ | reduz B→B*C |
| $A | < | + ( c ) d $ | empilha + |
| $A+ | < | ( c ) d $ | empilha ( |
| $A+( | < | c ) d $ | empilha c |
| $A+(c | > | ) d $ | reduz D→c |
| $A+(A | = | ) d $ | empilha ) |
| $A+(A) | erro2 | d $ | insere + (operador esperado) |
| $A+(A) | > | + d $ | reduz C→(A) |
| $A+A | > | + d $ | reduz A→A+B |
| $A | < | + d $ | empilha + |
| $A+ | < | d $ | empilha d |
| $A+d | > | $ | reduz D→d |
| $A+A | > | $ | reduz A→A+B |
| $A | — | $ | **aceita** |

**Método:** exatamente igual às outras questões de precedência — a única diferença é que aqui você precisa **notar os dois pontos onde falta operador** (entre `b` e `(`, e entre `)` e `d`) e aplicar o "erro 2" (inserir `+`) nesses pontos, continuando a análise normalmente depois.

---

### Questão Extra — Tabela preditiva

A prova traz FIRST/FOLLOW e a tabela M prontos (gramática `A→a|(B)`, `B→aD|(B)D`, `C→;AD`, `D→C|ε`). Para a simulação da entrada `(a; (;a);)`, aplique exatamente o algoritmo da seção 8.3 do guia principal: compare o topo da pilha com a entrada, use a tabela M para decidir a produção, empilhe o lado direito **invertido**, e use "sinc" para casar terminais. Sempre que a célula da tabela M estiver vazia para um não-terminal, aplique a rotina de erro do enunciado (aqui: erro 1 insere `a`, erro 2 insere `)`, erro 3 descarta símbolo).

---

## Tabela-resumo de todos os gabaritos

| Prova | Q1 (SIMPLE) | Q2 | Q3 | Q4 | Q5 | Q6 | Extra |
|---|---|---|---|---|---|---|---|
| 1º/2022 | MDC (dado) | **b) I e III** | **b) I e III** | **c) I e IV** | dado | dado | dado |
| 2º/2023 | MMC (dado) | **e) III e IV** | **c) II e III** | **d) II e IV** | dado | dado | dado |
| 2º/2024 | Tribonacci (dado) | dado | **d) Autômato de Pilha** | **b) I e III** | **e) T3 T3 T3** | **a) 10 30 20 10** | dado |
| 2º/2025 | **b) ambiguidade** | **b) fora do escopo** | Número perfeito (escrito acima) | **b) I e III** | **e) Esquema de Tradução = GLC+ações** | trace acima | método explicado |

---

## Observação final sobre as questões sem gabarito público

As questões marcadas com assertivas (I/II/III/IV) do Ybadoo — FIRST/FOLLOW, recursão/fatoração e escopo dinâmico — **não têm gabarito oficial divulgado**. Toda resposta acima foi recalculada do zero por mim, célula por célula, mostrando o raciocínio completo para você conferir e replicar. Se seu professor tiver outro gabarito, o mais importante é entender **o método de cálculo** (mostrado em detalhe em cada questão) — ele funciona para qualquer gramática nova que aparecer na sua prova de amanhã, mesmo que os símbolos mudem.
