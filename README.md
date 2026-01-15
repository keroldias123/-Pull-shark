# Trowdall - E-commerce SaaS Multi-Organização

Projeto monolítico SaaS RBAC multi-empresa, onde **organizações** publicam produtos e **clientes** compram, com controle completo de permissões, pagamentos, entregas e comissão da plataforma.

---

## Funcionalidades Principais

### 1. Multi-Organização (Tenancy)

- Cada organização possui seu **domínio, membros, produtos e categorias**.
- Configuração de **comissão da plataforma**.
- Membros com roles específicos (`ORG_ADMIN`, `ORG_MEMBER`).
- Convidar usuários para a organização.

### 2. Controle de Usuários (RBAC)

- Papéis: `PLATFORM_ADMIN`, `ORG_ADMIN`, `ORG_MEMBER`, `CUSTOMER`
- Policies baseadas em **abilities** (CASL compatível).
- Autorização dinâmica por usuário e organização.
- Sessões e autenticação segura.
- Suporte a contas externas (ex: GitHub).

### 3. Produtos e Categorias

- Categorias próprias por organização.
- Produtos com: nome, descrição, imagens, preço base, desconto.
- Cada produto pertence a uma categoria e a uma organização.
- Produtos ativos/inativos.
- Sistema de wishlist por cliente.

### 4. Pedidos (Orders)

- Cada pedido pertence a um cliente e a uma organização.
- Pedido contém itens com preço e desconto snapshot.
- Status do pedido: `WAITING_FOR_PAYMENT`, `PAYMENT_CONFIRMED`.
- Histórico de pedidos do cliente e da organização.

### 5. Entregas (Delivery)

- Organizações definem métodos de entrega com preço e tempo estimado.
- Cada pedido possui entrega associada.
- Suporta múltiplos serviços de entrega.

### 6. Pagamentos (Payment)

- Integração com gateways: Stripe, PayPal, MultiCaixa.
- Status: `PENDING`, `PAID`, `FAILED`, `REFUNDED`.
- Calcula automaticamente comissão da plataforma e valor líquido para a organização.
- Snapshot do valor pago, método e percentual da comissão.

### 7. Wishlist

- Clientes podem criar listas de produtos favoritos.
- Relacionamento com produtos multi-organização.

### 8. Notificações

- Suporte a e-mail, SMS, push (via integração futura).
- Templates configuráveis por organização.
- Eventos disparados: pedido pago, pedido cancelado.

### 9. Tokens e Recuperação de Senha

- Sistema de token para recuperação de senha (`PASSWORD_RECOVER`).
- Tokens com data de expiração.

### 10. Auditoria e Histórico

- Controle de alterações de produtos, pedidos e entregas.
- Histórico de pagamentos e status.
- Registro de membros, convites e roles.

### 11. Dashboard / Relatórios (Admin)

- Visualização de pedidos, faturamento e comissões.
- Visualização por organização ou global (PLATFORM_ADMIN).
- Estatísticas de vendas e entregas.

### 12. Segurança e Compliance

- RBAC centralizado e testável.
- Controle de acesso por organização e usuário.
- Validação de entradas via Zod.
- Password hashing e tokens JWT seguros.

### 13. Extensibilidade

- Estrutura modular para adicionar novos serviços ou integrações.
- Preparado para marketplace multi-vendedor futuro.
- Banco de dados relacional com Prisma ORM.

### 14. Tecnologias

- **Backend**: Node.js, TypeScript, Prisma, PostgreSQL/MySQL
- **Frontend**: Next.js (base Rocketseat RBAC) + Tailwind CSS
- **Autenticação**: NextAuth, JWT
- **Mensageria / Eventos**: RabbitMQ ou Kafka (para evolução futura)
- **Validação**: Zod
- **Controle de versão**: Git, monorepo opcional

---

## Fluxo da Aplicação

```mermaid
flowchart TD
    %% Atores
    PA[Platform Admin]
    OA[Org Admin]
    CL[Cliente]

    subgraph "Configuração (Tenancy)"
        OA -->|1. Cria/Edita| PROD[Produtos & Categorias]
        OA -->|2. Configura| SHIP_CONF[Métodos de Entrega]
        PA -->|3. Gerencia| ORG[Organizações]
    end

    subgraph "Experiência de Compra"
        CL -->|4. Navega| STORE[Loja da Organização]
        STORE -->|Exibe| PROD
        CL -->|5. Adiciona| CART[Carrinho / Wishlist]
        CART -->|6. Checkout| ORDER[Pedido]
    end

    subgraph "Financeiro & Processamento"
        ORDER -->|7. Aguarda Pagamento| PAY[Gateway de Pagamento]
        PAY -.->|webhook| SYSTEM[Sistema Trowdall]
        SYSTEM -->|8. Confirma Pagamento| STATUS_PAID[Status: PAID]
        SYSTEM -->|9. Split de Pagamento| COMISSION[Comissão + Valor Líquido]
    end

    subgraph "Logística"
        STATUS_PAID -->|10. Libera Envio| DELIVERY[Processo de Entrega]
        DELIVERY -->|11. Atualiza Rastreio| CL
    end
```

---

## Estrutura Recomendada

```text
src/
 ├─ auth/            # autenticação, RBAC
 ├─ organizations/   # gestão de organizações, membros, convites
 ├─ products/        # produtos, categorias
 ├─ orders/          # pedidos e itens
 ├─ delivery/        # métodos e pedidos de entrega
 ├─ payments/        # pagamentos e comissões
 ├─ customers/       # usuários finais, wishlists
 ├─ notifications/   # templates e disparo de eventos
 └─ utils/           # helpers, validações, logger
```

---

## Próximos Passos

- Implementar **migrations Prisma**
- Criar **abilities reais** com CASL
- Desenvolver **checkout com cálculo de comissão**
- Integrar gateways de pagamento
- Criar **interface do cliente e dashboard da organização**
- Adicionar notificações reais por e-mail/SMS

---

Este README serve como **documentação completa do projeto Trowdall**, pronto para começar o desenvolvimento com RBAC SaaS multi-empresa e funcionalidades de e-commerce completas.
