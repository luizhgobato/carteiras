# carteiras — regras para qualquer sessão Claude

Este repositório tem um único arquivo, `index.html`: um dashboard de investimentos
(Flavia, Luiz, Consolidada) publicado ao vivo no GitHub Pages
(luizhgobato.github.io/carteiras). Várias sessões Claude diferentes editam este
arquivo ao longo do tempo — todas aparecem como o mesmo autor "Claude" no git, então
não existe rastro claro de "quem fez o quê". Por isso estas regras.

## Regra principal: Qtd e PM são dados financeiros reais, não são seus para ajustar

As colunas **Qtd** (`class="cw-qtd"`) e **PM** (`class="cw-pm"`) dentro de
`#flaviaPosBody` e `#luizPosBody` representam a posição real do usuário na
corretora. **NUNCA** altere esses valores — nem "corrigindo", nem "sincronizando",
nem para bater com algum cálculo — **a menos que o usuário, NESTA mesma conversa,
tenha te dado a posição nova explicitamente** (print da corretora, dado de uma
operação de compra/venda, ou os números direto). Ver, ler ou calcular a partir de
Qtd/PM é sempre seguro; **escrever** neles exige essa autorização explícita.

Se você desconfiar que Qtd/PM estão desatualizados (ex.: o usuário reclamou, ou os
números parecem estranhos), **não corrija por conta própria** — pergunte, ou peça um
print atualizado. Já aconteceu de uma sessão presumir erroneamente que um ativo
tinha sido vendido e isso quebrou a confiança do usuário no projeto inteiro.

## O que É seguro atualizar sem pedir

- `.cw-preco`, `.cw-vardia`, `.cw-vardia-rs` — cotação e variação do dia, seja pela
  rotina diária automática (MCP de cotação) ou por um print de cotação atual que o
  usuário mandar. Isso é esperado e bem-vindo.
- `.cw-investido`, `.cw-saldo`, `.cw-result`, `.cw-pct-cart`, `.cw-pct-classe` —
  **nunca edite estas células à mão**. Elas se recalculam sozinhas no carregamento
  da página a partir de Qtd/PM/Preço (`_cwCalcular`, modo recálculo). Editar Qtd/PM
  ou Preço já é suficiente; essas colunas vão seguir.

## Antes de editar

1. `git fetch origin main` e comparar com `git log --oneline -1 origin/main` antes
   de começar — outras sessões podem ter pushado desde a última vez que você leu o
   arquivo.
2. Depois de editar, validar localmente (servidor HTTP + Playwright, headless) antes
   de commitar quando a mudança envolver JS/recálculo — não só olhar o HTML cru.
3. Commit direto em `main` (sem PR), mensagem descrevendo o que mudou e por quê.
4. Nunca amarrar Qtd/PM a uma suposição, "print antigo", ou extrapolação — se não
   tem certeza, é melhor perguntar do que aplicar um valor que pode estar errado.
