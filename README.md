# GoServ

Sistema de autoatendimento e pedidos para restaurantes e lanchonetes. O cliente escaneia o QR code da mesa, monta o pedido pelo próprio celular e paga por Pix. O pedido cai em tempo real na tela da cozinha. O dono administra cardápio, preços e relatórios por um painel web.

Projeto acadêmico, disciplina Sistema Visual, turma de quinta-feira, professora Lian Hua Liu Iwersen. Defesa de código em 19/11/2026.

## Equipe

| Integrante | Papel |
|---|---|
| Douglas Polak | Tech Lead / Arquiteto |
| Francisco Azevedo | Backend |
| Pedro Emanuel | DevOps / Infra e QA |
| Guilherme Godoi | Product Owner e Frontend |
| Bruno Brito | Requisitos / UX e Frontend |

Papéis definem responsabilidade principal, não exclusividade. Todo mundo programa.

## Arquitetura

O sistema é formado por **duas** aplicações React (não três) e uma API central em C#. A aplicação do cliente roda tanto no navegador do celular (acesso por QR code) quanto em modo quiosque num tablet Android; a diferença é uma configuração de implantação, não um código diferente. A aplicação interna reúne cozinha e administração sob o mesmo login, com telas renderizadas conforme o perfil do usuário autenticado.

A razão de ser só duas aplicações, e não três, está detalhada no [ADR-0001](docs/adr/0001-escolha-dotnet-postgresql-signalr.md) e no planejamento do projeto: a separação segue a fronteira de segurança (só o app exposto ao público fica fora da área autenticada), e reduz a superfície de build e de deploy.

```
Cliente (navegador ou tablet em modo quiosque)
        |
        v
apps/client  (React, público, sem login)
        |
apps/internal  (React, cozinha + admin, com login e SignalR)
        |
        v
   api/  (C# .NET 8 / ASP.NET Core)
        |
        v
   PostgreSQL (Entity Framework Core)
```

| Camada | Tecnologia | Responsável principal |
|---|---|---|
| apps/client | React | Guilherme e Douglas |
| apps/internal | React + SignalR | Bruno e Francisco |
| api | C# .NET 8 / ASP.NET Core | Francisco |
| Banco de dados | PostgreSQL + Entity Framework Core | Francisco e Pedro |
| Pagamento | API do Mercado Pago (Pix) | Francisco e Douglas |
| Infraestrutura | Cloud + GitHub Actions | Pedro |
| Testes | xUnit (backend) + checklist manual | Francisco e Pedro |

## Estrutura de pastas

```
goserv/
├── api/                    API C# .NET 8, em camadas
│   └── src/
│       ├── Controllers/    recebe a requisição HTTP
│       ├── Services/       regra de negócio
│       ├── Repositories/   acesso ao banco (PostgreSQL via EF Core)
│       └── Models/         entidades do domínio
├── apps/
│   ├── client/              app público: cardápio, carrinho, pagamento
│   └── internal/            app autenticado: cozinha (KDS) + admin
├── docs/
│   ├── adr/                 decisões arquiteturais registradas (ver seção abaixo)
│   └── BRANCHING.md         fluxo de branches e regras de Pull Request
└── .github/
    └── PULL_REQUEST_TEMPLATE.md
```

Todo endpoint da API segue o mesmo caminho: Controller recebe, Service decide a regra, Repository acessa o banco. Quem entende um endpoint entende todos.

## Como rodar o projeto do zero

*Seção a completar por Francisco (API) e por Guilherme/Bruno (apps React) assim que o esqueleto de cada parte subir ao repositório. Até lá, isto é o roteiro esperado:*

1. **Banco de dados**: subir PostgreSQL local (Docker) ou usar a instância na nuvem configurada por Pedro. Variáveis de conexão em `api/.env` (não versionado, ver `.env.example`).
2. **API**: `cd api && dotnet restore && dotnet run`. Sobe em `https://localhost:5001` (ajustar conforme configuração real).
3. **App cliente**: `cd apps/client && npm install && npm run dev`.
4. **App interna**: `cd apps/internal && npm install && npm run dev`.

Critério de pronto do time: se um integrante novo conseguir rodar o projeto do zero só lendo este README, o README está bom. Isso também é um dos quatro critérios oficiais de "cartão concluído" no Trello.

## Fluxo de branches e Pull Request

Ver [docs/BRANCHING.md](docs/BRANCHING.md).

## Decisões arquiteturais (ADR)

Toda decisão técnica relevante fica registrada em `docs/adr/`, no formato descrito em `docs/adr/template.md`. O primeiro registro é o [ADR-0001](docs/adr/0001-escolha-dotnet-postgresql-signalr.md), sobre a escolha de .NET, PostgreSQL e SignalR.

## Links úteis

- Quadro Trello: https://trello.com/invite/b/6a877e3dbe74a43db8d1a236/ATTIbe4adbefde8e97079a4f414b047b0ecbCE0E4EA2/aula-quinta-lian
- Compartilhado com: lian@up.edu.br
