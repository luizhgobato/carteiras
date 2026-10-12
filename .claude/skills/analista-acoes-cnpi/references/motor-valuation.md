# Motor de Valuation — Preço Justo

## Fórmula geral

```
Preço Justo = Fundamento projetado × Múltiplo-alvo
```

Onde o fundamento e o múltiplo dependem do tipo de negócio (seção seguinte), e o múltiplo-alvo vem sempre da própria história da empresa, ajustada por rentabilidade atual (seção "Ajuste por ROE").

Isto substituiu uma arquitetura anterior ("consenso de métodos" — mediana de vários métodos ao mesmo tempo) porque consenso de métodos é difícil de explicar e de auditar: quando dois métodos discordam, "a mediana dos dois" não responde por quê. Um método por empresa, escolhido pelo que o negócio é, com memória de cálculo auditável, é a escolha atual.

## Classificação por grupo de negócio

| Grupo | Método | Fundamento | Por quê |
|---|---|---|---|
| **Geral** (indústria, utilities, varejo, bancos, seguradoras) | **P/L** | LPA projetado do próximo exercício | regra padrão — lucro contábil é o fundamento mais direto quando não há distorção estrutural |
| **Shopping** | **P/FFO** | FFO projetado por ação | imóvel registrado a custo histórico distorce lucro contábil e patrimônio; FFO corrige isso |
| **Cíclica de commodity** | **EV/EBITDA sobre a média do ciclo** (mín. 3 anos, idealmente 5-7) | EBITDA médio de todo o ciclo disponível | lucro de 1 ano isolado é sempre pico ou fundo, nunca "normal" — a cíclica não corta a série pela metade, o ciclo inteiro é a amostra |
| **Holding pura** | **Paridade** | Preço Justo da investida principal (pelo próprio motor dela) × razão histórica preço-holding/preço-investida | lucro de holding é equivalência patrimonial — amplifica o ciclo da controlada sem acrescentar informação própria |

Bancos e seguradoras usam o mesmo método (P/L) mas **não compartilham pool de pares entre si** — banco ganha no spread de crédito, seguradora no resultado de subscrição/float, ciclos diferentes.

Se a empresa não se encaixar claramente em nenhum grupo, trate como caso geral (P/L) e diga isso explicitamente no relatório, em vez de forçar um encaixe.

## Múltiplo-alvo: mediana histórica da própria empresa

- **Janela**: desde 2021 até o exercício mais recente fechado. A escolha de 2021 não é arbitrária — foi quando a Selic brasileira voltou a dois dígitos; múltiplos negociados em 2011-2020 refletem um regime de custo de capital diferente e foram deliberadamente excluídos (testado: incluir anos mais antigos inflava os múltiplos entre +12% e +77%). Se a análise for feita muito à frente no tempo, considere com o usuário se a janela deve rolar (ex: "desde que a Selic voltou a dois dígitos" em vez de fixar 2021 pra sempre).
- **Estatística**: mediana pura, não média. Média deixa um único método ruim distorcer o resultado — caso real: uma mediana de métodos deu R$102 porque uma média simples incluiu um Gordon claramente defeituoso (R$68) ao lado de um EV/EBITDA razoável (R$136); nenhum dos dois métodos sozinho teria defendido R$102.
- **Exclusão de anos com quebra estrutural**: anos antes de uma mudança relevante de ações em circulação (fusão, follow-on, grupamento real — não cosmético) ou de um salto operacional sem mudança de ações (ex: privatização) ficam de fora da mediana.
- **Exclusão de anos não-operacionais**: margem líquida >100% por ganho não-recorrente (venda de ativo, fusão com ágio) é excluída — exceto em holdings puras, onde margem >100% é estrutural (vem de equivalência patrimonial) e não deve ser tratada como anomalia.
- **Peer fallback**: só quando a própria empresa tem menos de 3 anos utilizáveis. Exige no mínimo 3 pares do mesmo grupo; com menos que isso, cai pra mediana do universo inteiro coberto. Um único par nunca deve virar "mediana do setor" — é a idiossincrasia de uma empresa só disfarçada de referência setorial.
- **Override declarado**: se a própria empresa divulgou um múltiplo-alvo em relatório/guidance (ex: EV/EBITDA de meio-de-ciclo declarado pela própria companhia), isso vence a mediana calculada e **pula o ajuste de ROE** — a empresa já fez esse ajuste na própria declaração.

## Ajuste por ROE — mecanismo exato

```
fator = ROE atual ÷ ROE mediano do mesmo período (mediana pura, sem truncamento por tendência)
Múltiplo ajustado = Múltiplo histórico próprio × fator
```

**Sem teto.** Isto foi testado com um teto de ±30% e o teto foi removido no mesmo dia, por decisão explícita do usuário — mesmo com o registro da objeção de que a relação não necessariamente sustenta o múltiplo dobrar quando o ROE dobra. Hoje não existe piso nem teto no código.

⚠️ **Efeito colateral matemático que vale mostrar ao usuário quando relevante**: como o LPA projetado já embute ROE × VPA, multiplicar o P/L pela mesma razão de ROE faz o preço final escalar com o **quadrado** da razão:

```
Preço ≈ VPA × (P/VP histórico) × (ROE atual ÷ ROE mediano)²
```

Isso é conservador por construção na intenção original ("só pague o múltiplo alto se você acredita que o ROE realmente volta a esse patamar"), mas o efeito pode ser maior do que a intuição sugere quando a razão foge de 1,0. Ver `evidencias-e-fragilidades.md` para os dois casos documentados onde isso já causou um preço justo claramente problemático (SANB11, BBAS3) e para o checklist de quando avisar sobre isso.

Truncamento por tendência (usar só a metade recente da série quando a série tem uma tendência clara) é **deliberadamente desligado** nos pontos onde o ajuste de ROE atua — aplicar as duas correções juntas seria corrigir "o presente é diferente do passado" duas vezes.

## Detalhe por grupo

### Shopping — FFO
```
FFO = Lucro líquido + (EBITDA − EBIT)
```
Isto é uma **aproximação**, não o FFO oficial (NAREIT/ABRASCE) — não isola ganho de venda de ativo nem ajuste a valor justo, e por isso não é comparável diretamente entre empresas diferentes (uma deprecia a custo histórico, outra a valor justo — a proporção de D&A no EBITDA pode variar de ~6% a ~29% entre shoppings só por política contábil). Use só contra a série histórica da própria empresa, nunca pra comparar P/FFO entre shoppings diferentes sem ajustar por essa diferença de política.

### Cíclica de commodity — EV/EBITDA de ciclo
```
EV justo = EBITDA médio do ciclo × múltiplo-alvo
Preço Justo = (EV justo − Dívida Líquida atual) ÷ nº de ações em circulação
```
Exige no mínimo 3 anos de histórico utilizável; idealmente 5-7, cobrindo pico e fundo do ciclo. Descarte (ou marque baixa convicção) se o EBITDA do período variar mais que ~1,7x entre o maior e o menor ano — dispersão maior que isso geralmente indica que a amostra não cobre um ciclo completo, não que a empresa seja genuinamente instável nesse grau.

### Holding — paridade
```
Razão-alvo = mediana histórica (preço da holding ÷ preço da investida)
Preço Justo = Razão-alvo × Preço Justo da investida (não o preço de mercado dela)
```
Isto **não é um cálculo de NAV real** — ignora outros ativos da holding além da participação principal, ignora dívida no nível da holding, e herda qualquer erro de avaliação do motor aplicado à investida. Trate a convicção aqui como estruturalmente mais baixa que nos outros grupos, mesmo que a razão histórica pareça estável — avise o usuário dessa limitação sempre que avaliar uma holding.

## Margem de segurança

```
Margem = (Preço Justo − Cotação) ÷ Preço Justo
```

É **referência, não critério de aprovação/reprovação** — ver `motor-decisao.md` e `evidencias-e-fragilidades.md` para o porquê (instabilidade e falta de evidência como filtro).

## Avisos de qualidade de dado a checar sempre antes de confiar no número

- **Units** (ex: tickers com 11, como BPAC11, SANB11, KLBN11): confira se o P/L e o P/VP divulgados pela fonte são por unit ou por ação-base antes de usar — já houve caso de inflar o múltiplo em 3-5x por essa confusão.
- **Anos com margem líquida >100%** por evento não-recorrente (fusão, venda de ativo, ganho contábil pontual) devem ser excluídos da mediana — exceto em holdings, onde é estrutural.
- **Payout anual isolado é ruidoso em cíclicas**: um ano de lucro baixo infla o % distribuído artificialmente (visto chegar a >200% num único ano). Use payout mediano de vários anos, ou melhor: soma de dividendos pagos ÷ soma de lucros do período, não a mediana das razões anuais.
- **Tesouraria/ações em circulação**: confirme se o número de ações usado é o total emitido ou o free float — ações em tesouraria não têm direito a dividendo nem voto e, se contadas no denominador, subestimam o LPA real da empresa inteira.
