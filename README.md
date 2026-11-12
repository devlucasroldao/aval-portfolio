# ✓ Aval: aprovação de conteúdo para agências

> A agência manda um link, o cliente vê o feed do jeito que vai ficar e aprova cada post no celular. Com a marca da agência, não a do Aval.

[![Site ao vivo](https://img.shields.io/badge/Site%20ao%20vivo-meuaval.com.br-5B5CF0?style=for-the-badge&logo=vercel&logoColor=white)](https://meuaval.com.br)
[![Next.js](https://img.shields.io/badge/Next.js%2016-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![Supabase](https://img.shields.io/badge/Supabase-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white)](https://supabase.com)
[![Vercel](https://img.shields.io/badge/Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white)](https://vercel.com)

![Aval](imagens/capa.png)

> ⚠️ **Repositório de estudo de caso.** O Aval é um produto meu, em operação, e o código-fonte é privado. Aqui ficam o que ele faz, as decisões por trás e como ele foi construído. Todas as telas usam marcas fictícias.

---

## 📖 Sobre o projeto

O Aval nasceu dentro da Rabisco, a agência que toco com a Júlia. Toda semana era a mesma cena: oito artes mandadas no WhatsApp, o cliente respondendo "ficou ótimo" num áudio, pedindo pra mudar "aquela do fundo azul", e ninguém sabendo ao certo o que tinha sido aprovado.

Em vez de mais uma planilha, construí uma ferramenta. O cliente recebe um link, abre no celular sem instalar nada e sem criar conta, vê os posts da semana no formato do Instagram e aprova ou pede ajuste em cada um. A resposta chega na hora no painel da agência, com o histórico de quem aprovou e quando.

Fiz o produto de ponta a ponta, sozinho: interface, banco, segurança, landing, e-mails e lançamento. Hoje já tem agência usando no dia a dia.

## 📈 Em números

| | |
|---|---|
| Do primeiro commit às agências usando | 1 mês (13/10 → 12/11/2026) |
| Commits / pull requests | 260 commits, 50 PRs |
| Migrations do banco | 37, todas idempotentes |
| Teste de isolamento entre agências | 67 checagens, rodando no CI a cada mudança |
| Testes unitários | 150+ |

---

## 📱 O lado do cliente

![O cliente aprova no celular](imagens/cliente.png)

- **Prévia da semana:** o feed do jeito que vai ficar, com stories, carrossel e Reels. Aprovar ou pedir ajuste post por post, com a nota do que mudar.
- **Calendário do mês:** o planejamento num link só, com campanha, data e a descrição de cada item.
- **Peças impressas:** placa, outdoor, fachada, cardápio e folder no formato real, "na medida" (com uma silhueta de 1,70 m na mesma escala) e "no ambiente", com versões.
- **Relatório do mês:** o que foi produzido e aprovado, pra fechar o mês com o cliente sem planilha.
- **Pix:** cobrança com QR Code na mesma marca da agência.

Tudo abre num link com o logo, a cor e a fonte da agência (27 fontes), inclusive a capa do link no WhatsApp. O cliente não vê o nome Aval.

![Calendário do mês](imagens/calendario-desktop.png)

## 🗓️ O lado da agência

![Agenda da agência](imagens/agenda.png)

- **Agenda com todos os clientes:** etapa do briefing ao postado, responsável, filtro por pessoa da equipe e lembretes (do cliente ou da agência).
- **O mês inteiro de uma vez:** planejou no Claude ou no ChatGPT? Cola a resposta (JSON, tabela ou linhas de planilha) e confere linha por linha antes de entrar.
- **Equipe:** convite por e-mail, papéis e permissão separada pro financeiro.
- **Financeiro:** lançamentos, recorrências, gráfico do mês e cobrança por Pix.
- **Ferramentas do dia a dia:** dividir imagem pra carrossel, conversão de HEIC do iPhone e o mês inteiro num ZIP (pastas por semana e dia, com legendas e o calendário em planilha).
- **Onboarding sem atrito:** tour guiado que monta um cliente de exemplo, card de novidades e o painel instalável como app (PWA).

![Painel da agência](imagens/painel.png)

---

## 🛠️ Stack e decisões

![Por dentro](imagens/por-dentro.png)

| Camada | Tecnologia | Por quê |
|---|---|---|
| **Frontend** | Next.js 16 (App Router, Cache Components) e React 19 | Server Components e Server Actions deixam o painel rápido e o código de dados do lado do servidor |
| **Linguagem** | TypeScript | Um produto multi-agência não pode confundir de quem é cada dado |
| **Estilo** | Tailwind CSS 4 | Dois temas (painel claro e escuro) e a marca de cada agência aplicada por variáveis |
| **Banco** | Supabase (PostgreSQL, Auth, Storage) | Row Level Security nativa: o isolamento entre agências mora no banco, não só no código |
| **E-mail** | Resend, com domínio próprio | Convites e redefinição de senha com endereço da marca |
| **Deploy** | Vercel | Preview por pull request ligada a um banco de teste separado |

## 🔒 Multi-agência e segurança

Cada agência só enxerga os próprios dados, e isso é garantido pelo banco:

- **RLS em todas as tabelas,** com a agência ativa da sessão resolvida por uma função no próprio banco. A tela mostra exatamente o que o banco libera.
- **Chaves compostas** (agência + cliente) impedem que um item ou lembrete aponte pro cliente de outra agência, mesmo com um id válido.
- **Políticas restritivas de bloqueio:** agência bloqueada continua lendo e exportando, mas não grava, sem derrubar o link dos clientes dela.
- **Teste de isolamento no CI:** 67 checagens tentam ler e gravar dados de outra agência a cada pull request. Também reprova tabela nova sem permissão explícita.
- **Limite de tentativas no banco** pra login, convite e redefinição de senha; arquivos em bucket privado com links temporários.

*Uma auditoria de lançamento (23 achados: 3 altos, 10 médios, 10 baixos) foi feita antes de abrir pra agências de fora, e os pontos de segurança foram corrigidos. Detalhes de falhas não são publicados aqui, por princípio de divulgação responsável.*

## 🧭 Decisões de produto que valeram

- **Link em vez de app.** O cliente da agência não quer aprender ferramenta nova. Quer um link que abre no celular e um botão pra apertar.
- **White-label de verdade.** A marca da agência vai até a capa do link no WhatsApp. O Aval fica invisível pro cliente final.
- **A agenda reaproveita o calendário que já existia.** O que a agência planeja vira, sem retrabalho, o calendário que o cliente aprova.
- **Importar colando, não conectando.** Em vez de integrar com cada IA, o Aval lê a resposta colada e mostra o que entendeu antes de salvar.

## 🗄️ Modelo de dados (resumo)

```sql
agencies / agency_members / agency_invites   -- agências, equipe e convites
clients                                      -- clientes de cada agência
presentations / posts / stories              -- prévias da semana e respostas
calendars / calendar_items / agenda_reminders -- calendário do mês, agenda e lembretes
materials / material_pieces / material_versions -- peças impressas e versões
approval_events                              -- histórico de quem aprovou o quê e quando
reports                                      -- relatório do mês
finance_entries / recurring_charges / pix_keys / pix_charges -- financeiro e Pix
rate_limit_events                            -- limite de tentativas
```

---

## 🤖 IA como ferramenta de desenvolvimento

O Aval foi construído com Claude (Anthropic) como par de trabalho, com as decisões de produto e técnicas sob minha condução.

- **Testar antes de afirmar.** Toda migration roda num banco de teste antes da produção, e roda duas vezes pra provar que é idempotente.
- **Causa raiz.** Um exemplo: a tela "esticada" que vazava pra outras páginas não era CSS errado. O cache de rotas do Next 16 guardava a página anterior escondida no DOM, e um seletor `:has()` continuava enxergando o marcador dela.

---

## 👨‍💻 Contato

**Lucas Roldão** · [LinkedIn](https://www.linkedin.com/in/devlucasroldao/) · [Portfólio](https://devlucasroldao.vercel.app) · lucasroldao2802@gmail.com
