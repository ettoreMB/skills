---
name: pipeline-implementacao
description: "Pipeline fixo de implementação: acha o próximo passo, cria a branch, entrevista com grill-with-docs, formaliza em spec, quebra em tickets e implementa. A partir da spec, roda sem pausas até o PR aberto; code review só roda se pedido explicitamente depois."
disable-model-invocation: true
---

# Pipeline de Implementação

Sequência fixa para cada novo incremento do sistema, do "qual é o próximo passo" até o PR aberto. As etapas 1–3 fecham com o usuário antes de seguir; a partir da etapa 4 (spec) o pipeline roda sem parar para confirmação até o PR estar aberto na etapa 7.

Assume um projeto com roadmap/backlog documentado (ex.: `docs/roadmap-mvp.md` ou equivalente), ADRs/glossário de domínio (ex.: `docs/adr/`, `docs/CONTEXT.md`) e as skills `grill-with-docs`, `to-spec`, `to-tickets` e `implement` disponíveis. Ajuste os nomes de arquivo abaixo à convenção de cada projeto.

## Processo

1. **Próximo passo.** Leia o roadmap/backlog do projeto e o estado atual (commits recentes, issues abertas) para identificar qual etapa vem a seguir. Proponha esse próximo passo ao usuário em poucas frases e espere confirmação antes de seguir para a etapa 2.

2. **Branch.** Primeiro atualize a branch principal (`git checkout main && git pull`, ou `master`/`develop` conforme o repo), só depois crie e troque para a branch nova a partir dela. Nomeie seguindo a convenção do repo — `feat/etapa-N-nome-descritivo` (ver `git branch -a` para exemplos; use `fix/` ou `test/` quando o passo não for uma feature nova). Todo o trabalho das etapas seguintes (docs, spec, tickets, código) acontece nessa branch, nunca direto na branch principal.

3. **Grilling com docs.** Invoque a skill `mattpocock-skills:grill-with-docs` sobre o passo confirmado. Ela entrevista o usuário e atualiza ADRs/glossário do projeto conforme necessário.

4. **Spec.** Com as perguntas do grilling respondidas, invoque `mattpocock-skills:to-spec` para sintetizar a conversa numa spec e publicá-la no issue tracker. Sem pedir permissão, rode `/compact` em seguida e só depois siga para a etapa 5.

5. **Tickets.** Invoque `mattpocock-skills:to-tickets` para quebrar a spec em tickets de tracer bullet, publicados no mesmo tracker. Sem pedir permissão, rode `/compact` em seguida e só depois siga para a etapa 6.

6. **Implementação.** Invoque `mattpocock-skills:implement` para implementar os tickets, um após o outro, sem parar entre eles e sem rodar code review (ver guardrail abaixo). Ao terminar o último ticket da sequência, siga direto para a etapa 7 sem perguntar nada ao usuário.

7. **Push e PR.** Assim que todos os tickets estiverem implementados, dê `git push` da branch e abra o PR, sem pedir permissão. No corpo do PR, inclua `closes #N` para a issue da spec (etapa 4) e para cada ticket implementado (etapa 5), fechando todas automaticamente no merge.

Code review (`/code-review`, `/security-review`, ultrareview ou qualquer variante) não faz parte deste fluxo automático — só roda se o usuário pedir explicitamente, a qualquer momento depois do PR aberto.

## Guardrail: code review nunca roda por conta própria

A skill `implement` (etapa 6) termina chamando `/code-review` por conta própria — isso fica desligado dentro deste pipeline. Não rode code review entre tickets nem depois do PR aberto, a menos que o usuário peça explicitamente.

## Guardrail: verificação manual no Chrome só com permissão explícita

Ao validar uma mudança de frontend (etapa 6), não suba o servidor de dev do projeto nem dirija o browser com as ferramentas `mcp__claude-in-chrome__*` (plugin claude-in-chrome) por conta própria — pergunte antes se o usuário quer essa verificação manual no Chrome. Isso não se aplica a testes E2E automatizados (ex.: Playwright) escritos no código do repo: esses fazem parte da suíte normal e podem ser rodados livremente, junto com os demais testes automatizados (unitários, integração, typecheck e lint).

## Guardrail: push e PR ao final

O push e a abertura do PR (etapa 7) acontecem uma única vez, ao final de toda a sequência de tickets — nunca a cada ticket individual.
