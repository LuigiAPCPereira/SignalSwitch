# Signal Switch — brief e checkpoint inicial

Data: 2026-10-21. Estado: **identidade própria em implementação; não compilado/instalado; sem migração de código SignalRout**.

## Missão

Criar o **Signal Switch** como derivação leve do CC Switch, conservando Tauri 2, Rust e as funcionalidades já maduras de gerenciador de ferramentas de IA. Incorporar **apenas** ganhos comprovados do SignalRout e ideias seletivas de OmniRoute/9Router: políticas de roteamento, eficiência de tokens, cotas e observabilidade.

## Proveniência verificada

- Upstream: `https://github.com/farion1231/cc-switch.git`.
- Revisão inicialmente clonada: `cb90a2440d1a771d67f46b65b2e0f2ea5106ce85`.
- Branch de trabalho: `signal-switch/bootstrap`. Fork público GitHub: https://github.com/LuigiAPCPereira/SignalSwitch (confirmado como fork do `farion1231/cc-switch`). Remotos pretendidos: `origin` para o fork, `upstream` somente leitura/pull do original.
- Versão no `package.json` dessa revisão: `4.0.18`.
- Licença: MIT; manter copyright e avisos.
- Áreas de código existentes observadas: `src-tauri/src/proxy/{provider_router,circuit_breaker,cache_injector,sse}.rs` e `src-tauri/src/proxy/providers/{codex_oauth_auth,codex_responses_sse,streaming_responses}.rs`.
- A **presença desses arquivos não comprova** comportamento correto em condições reais; ainda faltam leitura aprofundada e testes.
- O SignalRout anteriormente desenvolvido **não foi encontrado neste ambiente**. Bloqueada sua migração até recuperar source/revisão/artefatos.

## Referências externas e ideias (não importadas)

| Fonte | Ideia a investigar | Limite de escopo |
|---|---|---|
| CC Switch | Configuração multi-CLI, proxy, conversão, OAuth, SSE, cache, circuit breaker, telemetria | Reaproveitar o existente; evitar reimplementar |
| SignalRout | Gates de compatibilidade Claude Code, ciclo de vida OAuth, isolamento, streaming, cancelamento e limites | Só portar diferenças após auditoria de código/testes |
| 9Router | Prioridades subscription → cheap → free, rastreamento de quota, fallback configurável | Sem inventar quota nem contornar autorização dos serviços |
| OmniRoute | Rotas combinadas, stickiness para cache, avaliações de compressão, orçamento por projeto | Começar pequeno; não copiar stack Next.js/Node/ML |
| RTK | Compactação de saída de comandos no lado do cliente | Prototipar com hooks antes de mudar payloads do proxy |

Fontes consultadas:
- https://github.com/farion1231/cc-switch
- https://github.com/decolua/9router/blob/master/gitbook/content/en/features/smart-routing.md
- https://github.com/rgplvr/omniroute
- https://github.com/rtk-ai/rtk

## Padrões de arquitetura propostos

1. **CC Switch permanece dono** de UI, configuração dos CLIs, storage, proxy e adaptação dos protocolos.
2. `routing-policy` (futuro): políticas determinísticas por provedor/modelo/tarefa; prioridades explícitas, health, cooldown e stickiness por sessão. Usar interfaces existentes sempre que possível.
3. `quota-budget` (futuro): distinguir quota **medida**, **declarada pelo provedor** e **estimada**; políticas de orçamento com falha segura. Rate limit não equivale automaticamente a quota total.
4. `token-efficiency` (futuro): baseline de contagem real e hooks RTK; apenas depois compressão recuperável, opcional e com teste de fidelidade. Não suprimir erros nem argumentos de ferramentas.
5. `observability` (futuro): taxas de cache, tokens reais, custo estimado, latência, escolha/failover e indicadores de estabilidade; sem segredos nem payload sensível por padrão.
6. Todos os novos módulos devem ser opcionais e preservar o caminho atual do proxy quando desligados.

## Sequência de fatias

### F0 — bootstrap, sem mudanças funcionais

- [x] Clonar upstream sem tocar no repositório original.
- [x] Criar branch isolada e registrar SHA/versão/licença.
- [x] Mapear arquivos candidatos do proxy.
- [x] Documentar missão, riscos e gates.
- [x] Criar **fork remoto real** na conta autorizada. Confirmar `origin` e manter `upstream` sem push.
- [ ] Recuperar fonte verificável do SignalRout.
- [ ] Validar build/testes baseline e medir RAM/CPU do CC Switch sem otimizações.

### F1 — auditoria diferencial

- [ ] Confrontar fluxos OAuth, Responses, SSE, tool use, cancelamento, segurança e limites entre SignalRout e CC Switch.
- [ ] Produzir matriz: já existe / diferença real / regressão / desconhecido, com arquivo e teste por linha.
- [ ] Selecionar **uma** melhoria do SignalRout que prove valor sem duplicação.

### F2 — roteamento e medição

- [ ] Protótipo de fallback por prioridade com cache-aware stickiness, opt-in e regras explícitas.
- [ ] Medição de cache/tokens/custos com baseline reproduzível.
- [ ] RTK externo via hooks (primeiro), medir economia e fidelidade.

### F3 — identidade e empacotamento

- [ ] Rebranding de UI e metadados sob identidade Signal Switch.
- [ ] Auditar package IDs, armazenamento, deep links, updates, signing, licença, telemetria e migração de configs.
- [ ] Distribuição local Linux e gates E2E antes de release.

## Gates de aceitação

- `git diff --check`, formatação, testes unitários e de integração relevantes passando na **mesma revisão**.
- Sessão real de Claude Code e Codex com mensagens, tools, SSE, cancelamento, retry limitado, erro upstream e redaction.
- Comparação de tokens e latência feita com corpus fixo, custo medido e fidelidade validada.
- Sem exposição de OAuth tokens, acesso por interface não-loopback inesperada ou perda de configurações existentes.
- Após alterações de identidade, sem auto-update do upstream nem sobreposição indevida de arquivos/configurações.

## Bloqueios atuais

1. Fork autenticado criado na conta `LuigiAPCPereira`; a publicação de branch/commit local requer verificação separada.
2. SignalRout não está no workspace conectado; **nenhuma implementação foi migrada**.
3. CI, build, OAuth real, SSE e benchmarks **não executados** nesta etapa.

## Próxima fatia exata

Recuperar o SignalRout em fonte durável e comparar um único fluxo crítico (preferencialmente SSE/Responses ou lifecycle OAuth) contra o CC Switch antes de alterar o proxy. O fork GitHub já existe.

## Checkpoint de identidade e distribuição (2026-10-21)

- Em andamento: nome Signal Switch, bundle ID `io.github.luigiapcppereira.signalswitch`, package Cargo/JS, URL scheme `signalswitch://`, autostart/tray independentes.
- Diretório de dados default: `~/.signal-switch/`; arquivos `signal-switch.db`, configurações e snapshots próprios. Configurações de Claude/Codex/OpenCode são **externas e compartilhadas por design**: ativar o proxy ou trocar provedores modifica o cliente; não executar isso automaticamente para migração.
- Auto-updater upstream desabilitado, sem chave do fork: atualização manual até configurar assinatura e feed próprios. O painel de update poderá informar indisponibilidade.
- Sync remoto default: `signal-switch-sync`, evitando compartilhamento acidental do remote root do CC Switch.
- Ícones e textos legados ainda podem representar o CC Switch; rebranding gráfico é fase posterior. Nenhuma migração automática de `~/.cc-switch`.
- Integração de releases Linux pendente de build/validação no host; não publicar artefatos antes de validar dependências e startup.

## Checkpoint de validação — 2026-10-22

- Auditado o caminho real de banco (`database/mod.rs`, `database/backup.rs`, `config.rs`): as rotinas de inicialização, backup e fallback do Signal Switch usam agora `signal-switch.db`, dentro do diretório próprio. Também foi corrigida a nomenclatura de backups legados e testes relacionados.
- ACL `updater:default` removida, dependência updater removida, endpoint/chave upstream ausentes e comandos de atualização fail-closed. A interface informa que a atualização automática não está configurada; não distribuir atualizador até existir chave própria e canal assinado.
- Frontend: `pnpm typecheck` passou e `pnpm build:renderer` passou; o bundler informou apenas avisos de chunks grandes. `cargo fmt --manifest-path src-tauri/Cargo.toml --all` executado; `git diff --check` passou.
- Compilação Release iniciada em `fish -lc 'pnpm tauri build --no-bundle'`; log de compilação no host: `/tmp/signal-switch-release-build.log`, sessão da ferramenta: `29390`. O término, resultado de compilação Rust, testes Rust e instalação **não estavam confirmados** no momento deste registro.
- **Não iniciar aplicativo em conjunto com CC Switch** sem revisar a escrita compartilhada das configurações de clientes Claude/Codex. Dados privados Signal Switch separados, mas os CLIs permanecem recursos externos compartilhados por projeto.
- Próxima ação exata: reconciliar o estado do build Release na sessão/log, corrigir se houver erro, validar binário e fazer smoke test antes de instalar desktop entry local. Não supor que a sessão de build continua viva entre conversas.
