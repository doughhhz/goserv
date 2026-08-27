# ADR-0001: Uso de .NET 8, PostgreSQL e SignalR como stack principal do backend

Status: aceito
Data: 2026-08-27
Decidido por: equipe GoServ (Douglas, Francisco, Pedro, Guilherme, Bruno)

## Contexto

O GoServ precisa de uma API backend que sirva duas aplicações React (uma pública, sem login, e uma interna, com login e diferentes perfis), processe pagamento via Pix, e entregue pedidos em tempo real para o painel da cozinha assim que o pagamento é confirmado. O projeto acadêmico tem prazo fixo (12 aulas, de 20/08 a 12/11/2026, defesa em 19/11), equipe pequena de 5 pessoas com um único desenvolvedor backend dedicado (Francisco, com apoio de revisão de Douglas), e a exigência explícita de que qualquer integrante consiga explicar qualquer parte do sistema na defesa.

Isso pesa a favor de tecnologias com pouca configuração invisível, documentação abundante em português, e onde uma escolha "padrão de mercado" já resolve o problema sem precisar somar peças extras.

## Decisão

**Backend em C# com .NET 8 / ASP.NET Core.**
É a linguagem que a equipe já definiu usar no projeto e em que Francisco tem mais familiaridade. ASP.NET Core tem geração de projeto Web API pronta, suporte nativo a SignalR (mesma stack, sem integração externa) e um caminho direto para autenticação com JWT, que o projeto já precisa (RF10, perfis distintos para cozinha e administração).

**Banco de dados PostgreSQL, acessado via Entity Framework Core.**
Banco relacional resolve bem o domínio do GoServ (produtos, categorias, pedidos, itens de pedido, sessões de mesa), que é fundamentalmente relacional, com relações claras entre entidades. Tem camada gratuita suficiente para o porte do MVP em provedores cloud comuns, e Entity Framework Core reduz a necessidade de escrever SQL manual para a maior parte dos casos, o que ajuda a manter o código legível para toda a equipe, não só para quem já manja de banco.

**Tempo real com SignalR.**
O requisito de "pedido aparece na cozinha sem recarregar a tela" (RF04) é exatamente o caso de uso do SignalR: comunicação em tempo real sobre WebSocket, nativa do ecossistema .NET, sem precisar de um serviço de mensageria separado.

## Alternativas consideradas

**Cache distribuído com Redis, para fila e tempo real.**
Avaliado e descartado. PostgreSQL e SignalR já atendem o requisito de tempo real do MVP; Redis seria uma dependência de infraestrutura adicional (mais uma peça para Pedro manter no deploy, mais uma coisa para a equipe entender na defesa) sem ganho funcional visível no volume de pedidos esperado num piloto de um único estabelecimento. Fica registrado como possível item de v2, se o volume de pedidos simultâneos justificar.

**Node.js/Express no backend, para unificar a linguagem com o frontend React.**
Descartado porque a equipe já tinha Francisco com mais experiência prática em C#/.NET do que em backend Node, e o projeto já usa .NET como padrão definido antes da entrada neste documento. Trocar agora custaria a familiaridade já construída sem ganho claro.

**Banco NoSQL (ex.: MongoDB).**
Descartado porque o domínio do GoServ é relacional por natureza (pedido tem itens, item referencia produto, produto tem categoria, sessão de mesa referencia pedidos). Modelar isso em documento aninhado tende a duplicar dado e complicar justamente as consultas que os relatórios (RF08) precisam fazer.

## Consequências

**Facilita:** um único ecossistema (.NET) cobre API, autenticação, tempo real e acesso a dados, o que reduz o número de tecnologias diferentes que a equipe precisa saber explicar na defesa. PostgreSQL gerenciado tira de Pedro a necessidade de administrar réplica ou cluster manualmente no porte atual do projeto.

**Custa/limita:** se o volume de pedidos simultâneos crescer muito além do piloto de um estabelecimento, SignalR sem um backplane (como Redis) tem limite de escala horizontal; isso é aceitável para o MVP acadêmico e está registrado aqui para não ser esquecido caso o projeto continue além da disciplina. A escolha por EF Core também abstrai parte do SQL, o que exige atenção redobrada em consultas de relatório (RF08) para não gerar N+1 queries silenciosas; fica como item de atenção para revisão de código nessa área.
