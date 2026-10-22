# Signal Switch — instruções para agentes

Este workspace é um **fork de desenvolvimento** do CC Switch, vinculado à conta GitHub autorizada em https://github.com/LuigiAPCPereira/SignalSwitch. O código-base permanece inalterado até a implementação de melhorias verificadas.

## Fontes de verdade

1. O código neste repositório e o SHA atual são a base verificável.
2. `docs/signal-switch/PROJECT_BRIEF.md` contém escopo, decisões preliminares, riscos e fronteiras da primeira fase.
3. A origem do SignalRout deve ser recuperada do repositório real antes de qualquer afirmação sobre implementação ou migração. Memória de conversas não comprova o estado do código.
4. CC Switch: https://github.com/farion1231/cc-switch (remote `upstream`).
   Fork: https://github.com/LuigiAPCPereira/SignalSwitch (remote `origin`).
5. OmniRoute e 9Router são **referências de comportamento**, não fontes autorizadas de cópia de código sem revisão das licenças e dependências.

## Princípios

- Agent Protocol: evidence-first, mudança pequena e reversível, teste da revisão exata.
- Não substituir silenciosamente OAuth, SSE, protocolo, sessões ou configuração que já existam no CC Switch.
- Preservar upstream e trabalho do usuário. Não fazer push, release, merge, publicação ou mudança de credenciais sem destino e estado verificados.
- Separar **documentado**, **implementado**, **observado** e **validado**.
- Não registrar tokens, segredos, conteúdos sensíveis de prompts ou cabeçalhos de autorização nos logs.
- Toda otimização de tokens deve preservar fidelidade; iniciar com medição e modo opt-in/fail-open.
- Não contornar limites, autenticação ou termos de serviços dos provedores.
- Evitar servidores residentes extras e dependências de ML para o caminho crítico sem benchmarks reais.
- Manter avisos de copyright e licença MIT do projeto original; rebranding deve incluir auditoria dos identificadores, deep links, updater e assinatura.

## Fluxo de trabalho

1. Ler o brief e recuperar o estado da branch.
2. Conferir o código upstream atual; priorizar extensão em módulos existentes.
3. Comparar a implementação real do SignalRout apenas após receber acesso à sua fonte original.
4. Propor uma fatia vertical com critérios de aceitação, implementar e rodar gates aplicáveis.
5. Registrar evidência, SHA e pendências no brief antes de encerrar.
