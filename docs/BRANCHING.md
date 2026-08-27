# Fluxo de branches e Pull Request

Fluxo simples de propósito: com 5 pessoas e 12 aulas, a complexidade de um fluxo tipo Git Flow completo (develop, release, hotfix) custa mais do que ajuda. Usamos um trunk-based simplificado: uma branch principal sempre estável, e branches curtas de tarefa.

## Branches

**`main`**
Sempre em estado deployável. Protegida: ninguém sobe direto nela. Todo merge dispara o pipeline (build, lint, testes) e o deploy automático no ambiente de testes, conforme configurado por Pedro.

**Branches de tarefa**, uma por cartão do Trello, nomeadas assim:

```
<tipo>/<área>-<descrição-curta>
```

Tipos usados: `feature`, `fix`, `chore`, `docs`, `test`.
Área: `api`, `client`, `internal`, `infra`, `docs` (ajuda a saber de longe o que a branch mexe).

Exemplos:
- `feature/api-crud-produtos`
- `feature/client-carrinho`
- `fix/internal-status-pedido`
- `docs/adr-escolha-stack`
- `chore/infra-pipeline-ci`

A branch nasce da `main` atualizada e morre quando o Pull Request é mergeado. Não existe branch de longa duração além da `main`.

## Pull Request

1. Abra o PR assim que a tarefa estiver pronta para revisão (não espere acumular várias tarefas num PR só; PR pequeno é revisado mais rápido e é mais fácil de entender na defesa).
2. Preencha o template (`.github/PULL_REQUEST_TEMPLATE.md`): o quê foi feito e por quê, em linguagem simples, do jeito que você explicaria pra um colega que não viu o código.
3. Peça revisão de outra pessoa da equipe. O Tech Lead (Douglas) revisa por padrão, mas qualquer integrante pode revisar, e revisar código de outras áreas é bem-vindo (aprender uma área diferente da sua é justamente o que a defesa vai cobrar).
4. Prazo de revisão: até 48 horas. Se estourar o prazo, qualquer outro integrante pode assumir a revisão, para o PR não travar o time.
5. O revisor só aprova se realmente entendeu o que o código faz. Aprovar sem entender derrota o propósito da revisão, que é justamente também espalhar conhecimento do sistema pela equipe.
6. Depois de aprovado e com o pipeline passando, qualquer um dos dois (autor ou revisor) pode mergear.

## Definição de "cartão concluído" (vem do planejamento oficial do projeto)

Um cartão do Trello só vai para "Concluído" quando as quatro condições abaixo forem verdadeiras ao mesmo tempo:

- O Pull Request foi revisado e aprovado por outro integrante.
- O código foi integrado à `main`, com o pipeline passando.
- O projeto roda do zero seguindo só o que está escrito no README.
- A funcionalidade foi testada por alguém que não a desenvolveu.

## Matriz de revisão cruzada

A ideia (definida no planejamento): quem desenvolve backend também revisa frontend, e vice-versa, para que nenhuma entrega grande tenha o mesmo integrante como autor e único conhecedor.

A definição de quem revisa o quê é decidida em equipe na Aula 2, junto com a fatia vertical de cada integrante. Preencher aqui depois dessa reunião:

| Área | Desenvolve principalmente | Revisa principalmente |
|---|---|---|
| api | Francisco | *(a definir na Aula 2)* |
| apps/client | Guilherme, Douglas | *(a definir na Aula 2)* |
| apps/internal | Bruno, Francisco | *(a definir na Aula 2)* |
| infra / CI-CD | Pedro | *(a definir na Aula 2)* |
