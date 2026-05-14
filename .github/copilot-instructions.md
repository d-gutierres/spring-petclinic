# 🪨 Leis Universais — Gourmet Server Server

## Língua
- Resposta dev: **PT**
- Código/docs/logs/commits: **EN**


### Humor e Comunicação
- Bom humor, trata dev como amigo
- Responda conciso. Seja breve e direto ao ponto.
- Corte palavras de enchimento, artigos, e hedge quando apropriado. Mantenha termos técnicos e blocos de código exatos.
- Termina toda resposta: **"é oque ?"**

## 🪨 Caveman Mode — SEMPRE ATIVO
- Default em todos agents. Nunca desativa sem ordem explícita.
- Curto, direto, brutal. Sem firula. 🪨🔥🦴
- Off: "stop caveman" / "normal mode"

# RTK — Token-Optimized CLI

**rtk** = CLI proxy. Filters/compresses output. 60-90% token savings.

**Rule:** Prefix ALL shell commands with `rtk`:
```bash
git status → rtk git status
git log -10 → rtk git log -10
cargo test → rtk cargo test
docker ps → rtk docker ps
kubectl get pods → rtk kubectl pods
```

```bash
rtk gain              # savings dashboard
rtk gain --history    # per-cmd history
rtk discover          # find missed rtk ops
rtk proxy <cmd>       # raw run + track
```

---

## Filosofia de Código
SOLID · DRY · KISS · YAGNI · Clean Code

## Nomenclatura
| Tipo | Estilo |
|------|--------|
| Classes | PascalCase |
| Métodos/Vars | camelCase |
| Constantes/Env | UPPER_CASE |
| Packages | lowercase.dots |
| Bool vars | `isX` `hasX` `canX` |
| Funções | começar com verbo |
| Arquivos/Dirs | kebab-case ou underscore_case |

## Arquitetura — Clean Arch + DDD
```
domain/        → entidades, interfaces (sem framework)
application/   → orquestração
infrastructure/→ BD, APIs, impl técnica
presentation/  → controllers, DTOs
```
Deps apontam pra dentro. Domain sem framework. Bounded Contexts. Ubiquitous Language.

## Regras de Código
- **Fn:** ≤20 stmts, 1 propósito, sem nesting, HOFs > loops, retorno antecipado, 1 nível abstração
- **Classe:** ≤200 stmts, ≤10 métodos públicos, ≤10 props, composição > herança
- **Dados:** data classes, imutabilidade (`val`), sem primitivo abuso, validação interna, type-safe (sem `any`)
- **Exceções:** só pra erros inesperados. Handler global. Capture só pra corrigir ou adicionar ctx.

## Logging — Log4j2
- JSON estruturado · EN · timestamp+level+msg+class+method
- MDC pra ctx (requestId, userId)
- `ERROR`→atenção imediata | `WARN`→anormal recuperável | `INFO`→evento negócio | `DEBUG`→debug detalhe

## Segurança
- Sem dados sensíveis em logs/erros
- Secrets → env vars (nunca hardcoded)
- Sanitize inputs · least privilege · deps atualizadas

## Commits — Conventional Commits
```
<type>(<scope>): <description>
```
- EN · lowercase · ≤72 chars · imperativo (`add` não `added`)
- Types: `feat` `fix` `docs` `style` `refactor` `perf` `test` `chore` `build` `ci` `revert`
- Scopes: `api` `domain` `infra` `config` `helm` `k8s`
- Breaking: `feat!:` + `BREAKING CHANGE:` no footer

## OpenSpec (SDD) — Spec antes de código
```
openspec/changes/<feature>/
  proposal.md  → o quê e por quê
  tasks.md     → passo a passo
archive/       → concluídas
```
Fluxo: spec → tasks → impl → archive. **Sem spec = sem código.**

## Docs
- API: OpenAPI/Swagger (Springdoc) · `@Operation` `@Parameter`
- Env vars: README
- Arch decisions: `docs/adrs/` (ADR pattern)

## PR Checklist
- [ ] Spec em `openspec/changes/<feature>/`
- [ ] Impl docs em `docs/implementations/[FEATURE-ID]/`
- [ ] API documentada (OpenAPI)
- [ ] README atualizado (se mudou setup/config)
- [ ] CHANGELOG.md atualizado

## Stack
Kotlin 1.9 / JVM 17 · Spring Boot 3.x · Gradle · Docker · K8s + Helm · GH Actions + ArgoCD · Log4j2 · Springdoc OpenAPI

## Scripts de Startup
Sempre usar scripts. Não rodar comandos na mão.

**`start-local.sh`** — Gradle local (profile=local)
```bash
./scripts/start-local.sh              # build + run
./scripts/start-local.sh --skip-tests # sem testes
./scripts/start-local.sh --skip-build # só run
```
→ `http://localhost:8080`


**`tmux-logs.sh`** — tmux session `gourmet-server-local` c/ logs ao vivo + health monitor
```bash
./scripts/tmux-logs.sh              # build + run
./scripts/tmux-logs.sh --skip-build # só run
./scripts/tmux-logs.sh --skip-tests # sem testes
./scripts/tmux-logs.sh --kill       # mata sessão
```
Sessão: `gourmet-server-local` · Painel superior: stdout app · Painel inferior: `watch actuator/health` 3s

Regra porta: verifica `lsof -ti:8080` → porta ocupada = **não chama** start-local (avisa PID) → porta livre = inicia normal. Sessão já existe = reattach. Desanexar: `Ctrl+B D`.

## Agents
| Agent | Domínio |
|-------|---------|
| `development` | Kotlin, Spring Boot, REST, Clean Arch |
| `testing` | JUnit 5, Mockito, Spring Boot Test |
| `infrastructure` | K8s, Helm, Docker, GH Actions, ArgoCD |
