# Motor de Decisão — TIR Real, Critérios, Veredicto

## TIR real: três medidas independentes

Todas expressas como **retorno real anual** (já descontada a inflação), comparadas contra a **NTN-B** (título público indexado ao IPCA — a taxa real livre de risco de referência).

### Conversão nominal → real: sempre Fisher, nunca subtração

```
CORRETO:  real = (1 + nominal) ÷ (1 + IPCA) − 1
ERRADO:   real = nominal − IPCA
```

A subtração simples superestima o retorno real (erro medido de ~0,8 p.p. em taxas na casa de 17%, maior em taxas mais altas). Use sempre a conversão correta.

### 1. Caixa (FCFE)

```
FCFE = FCO − Capex + Δ Dívida Bruta (bruta, não líquida — evita contar caixa em dobro)
TIR nominal = FCFE ÷ Valor de Mercado
TIR real = conversão de Fisher acima
```

Use a variação da **dívida bruta** entre os dois últimos exercícios fechados (depois de qualquer quebra estrutural), não a dívida líquida — dívida líquida já desconta caixa, e o FCO já é um fluxo de caixa, então usar dívida líquida contaria o caixa da empresa duas vezes.

Esta medida só é computável quando a empresa discrimina FCO e capex com clareza — não force quando os dados não sustentam; é normal faltar essa medida em várias empresas e sobrar só as outras duas.

### 2. Dividendos (DDM — Modelo de Desconto de Dividendos)

10 anos de dividendos crescendo à taxa `g` (ver abaixo), depois perpetuidade à taxa `IPCA + 2%`. A TIR é a taxa que iguala o preço pago hoje ao valor presente dessa série — resolva por busca binária (não há fórmula fechada com crescimento em dois estágios).

Esta é geralmente a única medida que funciona bem para financeiras, que tipicamente não têm um FCFE limpo nem um lucro "normalizável" pelo método de receita×margem.

### 3. Lucro normalizado

```
TIR nominal ≈ (Lucro normalizado ÷ Valor de Mercado) + g
TIR real = conversão de Fisher
```

O "lucro normalizado" depende do tipo de negócio:

| Setor | Normalizador |
|---|---|
| Indústria / serviço | Receita × margem líquida mediana histórica |
| Banco / seguradora | ROE mediano histórico × Patrimônio Líquido atual |
| Shopping | FCO − Capex (mesma lógica do FFO) |

Evite usar um "lucro recorrente" divulgado pela própria base de dados sem conferir — esse campo costuma ser reconstruído de forma indireta (reverso de impairments/itens não-recorrentes) e pode estar simplesmente errado para setores com muita "outra receita operacional" legítima (caso de bancos: o campo pode remover receita que é normal do negócio, não um evento isolado). Confira contra ROE × Patrimônio Líquido antes de confiar nele.

### `g` — taxa de crescimento usada na TIR

```
g = MÍNIMO( regressão log do lucro recorrente sobre a série, ROE × taxa de retenção )
    com piso em 0% e teto em 15%
```

**Por que regressão log e não CAGR, média ou mediana simples**: CAGR usa só os dois pontos extremos da série — um ano atipicamente bom ou ruim numa das pontas distorce tudo. Média aritmética de variações ano a ano tem viés de Jensen (sistematicamente superestima o crescimento composto real — casos medidos de superestimar em até 2x). Mediana esconde declínio monotônico (uma empresa perdendo lucro consistentemente por 4 anos pode aparecer como "estável" na mediana). Regressão log usa a série inteira e captura a tendência real.

**ROE × retenção tende a inflar** — assume reinvestimento perpétuo ao ROE de hoje, o que raramente se sustenta. Prefira a regressão log do lucro recorrente quando disponível; use ROE×retenção como teto/cross-check, não como primeira escolha.

**Bancos e seguradoras não têm um "lucro recorrente" confiável pra regressão** — pra esses, `g` normalmente só pode vir de ROE×retenção. Trate isso como premissa mais fraca que nos demais setores, e diga isso no relatório quando relevante.

**Piso em 0%**: existe porque, sem ele, o modelo projetaria queda por 10 anos seguida de uma perpetuidade positiva sem nada que justifique a virada — isso é premissa conservadora declarada, não uma medição de que a empresa vai estagnar (não crescer, mas também não encolher mais).

**Cíclicas de commodity não devem usar a regressão de lucro pra `g`** — ela capta só a queda até o fundo do ciclo mais recente, produzindo taxas de crescimento absurdamente negativas. Use só ROE×retenção pra esse grupo.

### Como ler e mostrar a faixa

Sempre mostre **mín–máx das três medidas**, não só a mediana:
- Faixa inteira acima da NTN-B → sinal forte de compra
- Faixa inteira abaixo → descarta
- Faixa cruzando a NTN-B → "precisa de leitura, a tabela não decide sozinha"
- Faixa estreita → número confiável, seja ele bom ou ruim
- Faixa larga → a própria discordância entre as três medidas é a informação relevante, não um detalhe a esconder

A **mediana** das três (não o topo — o topo é sempre a medida mais otimista das três e classificaria quase tudo como "aguardar", o que não discrimina nada) é o que decide o veredicto por baixo dos panos (ver seção seguinte), mas mostre a faixa inteira no relatório.

## Os quatro critérios

| # | Critério | Corte de referência | Observação |
|---|---|---|---|
| 1 | Earnings yield (L/P = LPA LTM ÷ cotação) ≥ Selic | Selic vigente | "Se esta empresa nunca mais crescer e eu comprar a este preço, ganho da renda fixa?" Prefira o LPA **implícito** (preço de fechamento ÷ P/L do mesmo exercício, de uma fonte só) a um LPA digitado à mão de fonte secundária — LPA desatualizado já inverteu o resultado deste critério numa empresa real do projeto original. |
| 2 | ROE ≥ 15% | 15% | Abaixo do custo de capital típico, crescer destrói valor em vez de criar. |
| 3 | Dívida Líquida/EBITDA < 3x | 3x | **Não se aplica** a bancos, seguradoras e holdings — o passivo é a matéria-prima do negócio deles, não uma alavancagem no sentido operacional. Sai do denominador, não conta como reprovação. |
| 4 | Lucro projetado do próximo exercício ≥ lucro do exercício anterior | — | "Barato e encolhendo" é a armadilha de valor clássica — preço baixo sozinho não significa barato se o lucro está caindo junto. |

Um critério que não se aplica **sai do denominador** — não conta como reprovação nem como aprovação. Um banco, por exemplo, é avaliado em 3 critérios (1, 2, 4), não em 4.

A **margem de segurança não é um 5º critério** — é referência, mostrada separadamente, nunca somada à contagem de critérios aprovados. Ver `evidencias-e-fragilidades.md` para a evidência por trás dessa decisão.

## Veredicto

```
🟢 COMPRA    = margem de segurança ≥ 0  E  mediana da TIR real ≥ NTN-B  E  passa em TODOS os critérios aplicáveis
🟡 AGUARDAR  = mediana da TIR real ≥ NTN-B, mas falha em algo acima
🔴 ACIMA     = mediana da TIR real < NTN-B
```

Isto é **deliberadamente exigente no verde** — as três condições precisam bater juntas. Um veredicto de compra deve ser difícil de acender; não amacie nenhuma das três condições pra forçar um verde só porque a tese qualitativa parece boa. Se a tese é forte mas os números não fecham nas três condições, o veredicto correto é 🟡 ou 🔴 com a tese explicada — não force a cor pra combinar com a narrativa.
