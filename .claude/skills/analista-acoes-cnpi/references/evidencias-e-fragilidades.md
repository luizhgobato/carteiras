# Evidências e Fragilidades Conhecidas

Esta metodologia foi testada contra retorno real: 8 backtests separados, ~30 ações, 5 transições anuais (2021-2026). Isso é incomum — a maioria das metodologias de análise fundamentalista nunca passa por um teste desses. Mas os testes mostraram tanto o que funciona quanto o que não funciona, e **algumas decisões do motor atual contrariam a própria evidência medida**, por escolha explícita do usuário, não por descuido. Esta página existe pra você não repetir, sem avisar, um erro que o próprio projeto já cometeu e documentou.

**Ressalva geral válida para TODOS os números abaixo**: amostra pequena e com viés de sobrevivência (~30 ações que existem hoje — nenhuma faliu ou saiu da bolsa, porque se tivesse não estaria na lista observada). O n efetivo de significância estatística é o número de **anos** (~5), não o número de observações dentro de cada ano (que compartilham o mesmo choque de mercado). Nenhum teste cobriu um ciclo de Selic caindo. **O melhor resultado entre todos os testes (L/P puro, t=+2,63) nem bate a própria barra de significância a 5% que o projeto definiu** (|t|>2,78 com 4 graus de liberdade). Trate tudo abaixo como evidência direcional, nunca como prova — e diga isso ao usuário se ele tratar algum destes números como certeza.

## O que FUNCIONA (evidência mais forte disponível)

**Earnings yield (L/P) sozinho é o sinal mais forte medido em toda a bateria.** Terço mais barato por L/P rendeu 24,9% contra 16,1% do terço mais caro (spread +8,8 p.p., t=+2,63, 4 de 5 anos positivo) — o único teste de toda a bateria com |t|>2. Em setores defensivos (bancos/seguros/utilities/telecom) o spread foi ainda maior: +20,3 p.p., t=+2,52, 5 de 5 anos positivo.

**A margem de segurança funciona como PORTÃO (sim/não), não como RÉGUA (quanto mais, melhor).** Ficar abaixo do preço-teto rendeu +21,4 p.p. (setores defensivos) e +22,2 p.p. (toda a carteira observada) a mais que ficar acima. Mas **ordenar ações pela margem de segurança (a maior margem vence) deu spread NEGATIVO** (−10,7 p.p., acertou a direção em só 1 de 3 anos testados) — 50% de margem não rendeu mais que 10% de margem. **Implicação prática: use a margem pra decidir "compro ou não", nunca pra escolher "qual das duas ações está mais barata".**

**Um composto de 3-4 indicadores bate qualquer métrica isolada — mas um composto de 10 é o PIOR resultado de toda a bateria** (p=0,36, estatisticamente indistinguível de ruído). Poucos indicadores bons superam muitos indicadores misturados (bons e ruins).

## O que NÃO FUNCIONA (testado e descartado)

**Somar crescimento (`g`) ao earnings yield PIORA o resultado.** A TIR real completa (earnings yield + g) rendeu só +11,4 p.p. em setores defensivos — pior em 8,9 p.p. que o L/P puro sozinho (+20,3 p.p.). `g` isolado chegou a dar spread **negativo** (−10,3 p.p.) no grupo de controle (cíclicas/indústria/shopping/holding).

**Somar eixos de qualidade (ROE, alavancagem) ao L/P também piorou o resultado.** +21,9% (só L/P acima do corte) caiu para +20,0% ao exigir também ROE≥15% e alavancagem<3x simultaneamente.

**Combinar L/P + Dividend Yield por rank médio piorou o L/P puro.** De +8,8 p.p. (t=2,63) caiu pra +4,0 p.p. (t=0,81) — e não-monotônico (o terço do meio chegou a bater o terço mais barato num dos testes).

**Estrelas de convicção (★★★/★★/★), se você encontrar esse resíduo em algum relatório antigo do usuário, não carregam informação nenhuma.** ★★★ rendeu 18,3% no período testado; ★ rendeu 18,9% — praticamente invertido. Trate qualquer classificação por estrela como decoração, nunca como sinal.

**Uma nota 0-100 de "qualidade do investimento" foi removida do projeto original por falta de evidência** — mesmo problema das estrelas: comunicava uma precisão que o método nunca teve.

**EV/EBITDA teve o pior desempenho isolado entre os múltiplos testados** (spread −2,8 p.p.), contrariando a literatura clássica de value investing. A hipótese do próprio projeto é amostra pequena e viesada (só 89 observações válidas, bancos excluídos estruturalmente) — não necessariamente que EV/EBITDA seja um múltiplo ruim, mas que esta amostra específica não confirmou seu uso.

## O sinal que funciona muda por setor

Em bancos/seguros/utilities/telecom (defensivos): **L/P vence** (+20,3 p.p., t=+2,52).
Em cíclicas/indústria/shopping/holding (grupo de controle): **Dividend Yield vence** (+19,5 p.p., t=+3,89), e o L/P cai pra apenas +8,0 p.p.
`g` é ruim nos dois grupos — neutro/ruidoso em defensivos, **negativo** no grupo de controle.

Racional econômico proposto pelo projeto (não testado à parte, é interpretação): em setor regulado/estável o L/P é um sinal limpo de barateza; em cíclica/construção o dividendo é o sinal "revelado" — a empresa só paga o que de fato sobrou, então o próprio pagamento já filtra ruído contábil.

## Contradição conhecida: pares de setor vs. âncora da própria empresa

O motor usa **só** a mediana histórica da própria empresa pra definir o múltiplo-alvo (ver `motor-valuation.md`). Mas o próprio backtest dedicado a esta pergunta (54 observações, reconstrução ponto-no-tempo) mediu:

| Âncora testada | Spread | t | p-valor |
|---|---|---|---|
| **Média de (própria empresa + pares do setor)** | **+14,1 p.p.** | **+9,07** | **0,040** |
| Só pares do setor | +11,1 p.p. | +4,21 | 0,081 |
| **Só a própria empresa (o que o motor usa hoje)** | **−1,8 p.p.** | **−0,21** | **0,549** |

A combinação própria+pares é, de longe, o resultado mais estatisticamente forte de toda a bateria de testes do projeto — e é também o único resultado que o motor **decidiu não incorporar**, por argumento econômico qualitativo (exemplo citado: P/L de ~39x da própria história de um banco específico contra mediana de pares do setor em ~7,5x pareceu "emprestar precisão que a mistura não tem" demais pra aceitar sem questionar).

**Prática recomendada**: ao analisar uma ação, calcule informalmente o múltiplo mediano de 2-3 pares diretos do mesmo setor e mencione como um segundo sinal no relatório — não para substituir a conta oficial (que segue só a própria empresa), mas porque este é o ponto onde a metodologia mais claramente contraria sua própria evidência mais forte, e o usuário deveria ver essa segunda referência pra decidir se quer pesar nela.

## A fragilidade mais séria ainda sem solução: ajuste de ROE sem teto

O múltiplo-alvo é multiplicado pela razão (ROE atual ÷ ROE mediano histórico), **sem limite superior ou inferior**. Um teto de ±30% chegou a ser implementado e foi removido no mesmo dia por decisão explícita do usuário — mesmo com o registro, no próprio código, da objeção de que a relação economicamente não sustenta o múltiplo dobrar só porque o ROE dobrou.

Dois problemas conhecidos e **ainda sem solução** no projeto original:

**1. Efeito quadrático.** O LPA projetado já embute ROE × VPA. Multiplicar o P/L pela mesma razão de ROE faz o preço final escalar com o **quadrado** da razão:
```
Preço ≈ VPA × (P/VP histórico) × (ROE atual ÷ ROE mediano)²
```
Isso é conservador por construção na intenção original (só pague o múltiplo alto se você realmente acredita que o ROE sustenta esse patamar), mas o efeito numérico pode surpreender quem olha só o resultado final sem ver a conta — por isso a memória de cálculo (passo 3 do fluxo principal) deve sempre mostrar a razão de ROE explicitamente, não só o preço justo final.

**2. Autoanulação quando o declínio/recuperação é antigo.** Caso documentado no projeto original: se a queda (ou alta) de ROE de uma empresa já ocupa metade ou mais da janela histórica usada (hoje ~6 anos), a própria mediana "acompanha" o movimento, a razão se aproxima de 1,0, e o ajuste desaparece exatamente no momento em que deveria morder com mais força — o mecanismo se autoanula silenciosamente.

Caso extremo documentado: colapso de lucro de uma empresa específica (de ~R$30bi para ~R$12bi) acertou o preço justo **duas vezes** por este mecanismo — uma vez via LPA caindo, outra via o efeito quadrático do múltiplo caindo junto — produzindo um preço justo muito abaixo até da cotação de mercado mais deprimida (razão de ~0,16x o valor patrimonial contra ~0,68x de mercado). O próprio projeto deixou essa ação **sem relatório publicado**, com aviso explícito, sem decidir entre: reintroduzir um teto, trocar a base de lucro por uma normalizada/de ciclo, ou aceitar o número com ressalva.

**Quando aplicar este aviso**: sempre que a razão ROE atual ÷ ROE mediano estiver fora de aproximadamente 0,7–1,3, OU quando a empresa estiver no fim de um declínio/recuperação de lucro que já dura vários anos (perto do fim da janela histórica usada) — mostre a razão calculada explicitamente no relatório e sugira ao usuário considerar um teto informal (±30% é o valor que o próprio motor já usou antes de removê-lo) em vez de aceitar o valor bruto sem questionar.

## Limitações estruturais (o que esta metodologia deliberadamente não faz)

- **Não tem um DCF de verdade.** O único modelo de fluxo de caixa descontado que já existiu (DDM de dois estágios para financeiras) foi removido do cálculo de preço justo — sobrevive só como uma das três medidas de TIR real, nunca decide o preço sozinho.
- **Não tem nota de qualidade/moat sistemática.** Avaliação qualitativa de vantagem competitiva existe só como texto livre, e só nas empresas que têm relatório detalhado escrito à mão — não em todo o universo coberto, e não se traduz em nenhum número que entre no cálculo.
- **Não ajusta por governança ou critérios ESG.** Nenhum desconto ou prêmio por tier de listagem (Novo Mercado vs. demais), estrutura de controle ou critérios de sustentabilidade.
- **Não ajusta o múltiplo-alvo dinamicamente pelo ciclo de juros** além da escolha estática da janela 2021→hoje — essa escolha É uma forma de ajuste (evita que múltiplos de um regime de juros muito mais baixo vazem pro cálculo), mas é estática, não responde a mudanças de regime dentro da própria janela.
- **Não tem camada de construção de carteira.** Isto é avaliação de UMA ação por vez — tamanho de posição, correlação entre ativos, regra de rebalanceamento, disciplina de stop-loss: nada disso está aqui. Se o usuário perguntar "quanto devo alocar nisso", essa é uma pergunta diferente, fora do escopo desta metodologia — diga isso em vez de improvisar uma resposta.
- **Não roda Monte Carlo nem simulação probabilística.** Cenários (conservador/base/otimista) são três pontos fixos, não uma distribuição de probabilidade.
- **Não usa CAPM/beta de mercado.** O único custo de capital explícito que já existiu (era parte do DDM de financeiras) era uma constante ajustada por fatores observáveis declarados, não um beta de mercado — e esse mecanismo inteiro foi removido junto com o DDM.
- **Não trata risco cambial.** Relevante se a análise envolver um BDR — o projeto original teve pelo menos um caso (BDR com histórico curto) que acabou sem preço justo publicado por falta de dado suficiente, não por decisão metodológica sobre câmbio.

## Padrão de bug recorrente a vigiar: "duas fontes de verdade"

Se em algum momento da análise você perceber que dois números deveriam representar a mesma coisa mas não batem (ex: um payout usado no cálculo de dividendo diferente do payout citado em outro lugar do mesmo relatório), **pare e resolva a divergência antes de continuar** — não escolha um dos dois números arbitrariamente e siga em frente. Esse exato padrão de bug (duas rotinas calculando a mesma coisa de jeitos levemente diferentes, e a que "vence" é a que rodou por último, não a mais correta) se repetiu pelo menos sete vezes documentadas no projeto original.

## Checklist do passo 7 ("Pontos de atenção da metodologia")

Ao fechar uma análise, percorra esta lista e inclua no relatório **só** o que realmente se aplica a esta ação:

- [ ] Razão ROE atual/mediano fora de ~0,7–1,3? → avisar sobre o ajuste sem teto e mostrar a razão explicitamente
- [ ] Empresa em declínio ou recuperação de lucro há vários anos, perto do fim da janela histórica usada? → avisar sobre o risco de autoanulação do ajuste
- [ ] É cíclica, shopping ou holding? → avisar a limitação específica daquele motor (ciclo incompleto, FFO-proxy não comparável, paridade não é NAV real)
- [ ] Preço justo depende de menos de ~4 anos de histórico, ou a faixa p25-p75 do múltiplo é muito larga? → avisar baixa convicção, mesmo que o número final pareça preciso
- [ ] Foi comparado (ou caberia comparar) com pares do setor? → mencionar o achado de +14,1 p.p./p=0,040 não incorporado ao motor oficial
- [ ] A margem de segurança está sendo usada pra RANKEAR entre duas ou mais ações, não só decidir sim/não numa? → avisar que isso já foi testado e deu errado (spread −10,7 p.p.)
- [ ] Alguma fonte antiga trouxe estrelas de convicção ou nota 0-100? → avisar que isso não carrega informação medida
