# 💰 Sistema Financeiro Corporativo (Mini ERP)

![Status](https://img.shields.io/badge/Status-Concluído-success)
![Stack](https://img.shields.io/badge/Stack-C%23%20.NET%208%20%7C%20MVC-blueviolet)
![Database](https://img.shields.io/badge/Database-SQL%20Server%20%7C%20Dapper-informational)
![Arquitetura](https://img.shields.io/badge/Arquitetura-Multi--Tenant-orange)

> 🔒 **Aviso de Propriedade Intelectual:** Este é um repositório de demonstração (Showcase). Por se tratar de um sistema corporativo (Mini ERP) contendo regras de negócio financeiras sensíveis, modelagem multi-entidade e algoritmos proprietários de auditoria, o código-fonte está fechado. Este documento detalha a arquitetura, os desafios e o alto valor gerado pela aplicação.

## 💡 O Desafio Operacional e de Negócio
Instituições que lidam com repasses governamentais e grandes acordos de saúde (Organizações Sociais) enfrentam um desafio monumental: a **rigorosa prestação de contas**. O desafio era construir uma plataforma que não apenas registrasse entradas e saídas, mas que gerenciasse todo o ciclo de vida do recurso financeiro, garantindo rastreabilidade histórica, controle hierárquico de gastos e blindagem de dados entre múltiplas unidades/filiais.

## ⚙️ A Solução: Arquitetura e Engenharia
Desenvolvi um Mini ERP Multi-tenant robusto do zero utilizando a stack **C# .NET 8**, focado em altíssima performance e integridade de dados.

### 1. Motor Financeiro Hierárquico
O sistema blinda a operação forçando uma esteira lógica de consumo de recursos:
* **Instrumento:** O acordo macro (ex: Secretaria de Saúde).
* **Orçamento (Planejamento):** Dividido em níveis hierárquicos (Sintéticos e Analíticos).
* **Contrato (Compromisso):** "Consome" o saldo de rubricas analíticas específicas do orçamento.
* **Movimentação (Faturamento/Gasto):** Abate do saldo do contrato, refletindo no orçamento e, consequentemente, no instrumento.

### 2. Versionamento e "Máquina do Tempo" (O Coração do Sistema)
Lidar com "Aditivos" (de prazo, valor ou supressão) é a maior dor de cabeça de qualquer ERP. Para resolver isso, implementei um ecossistema de **Snapshots Históricos**:
* Cada alteração orçamentária ou contratual gera uma nova versão imutável (V1, V2, V3...) no banco de dados.
* **Vida Útil Global:** Algoritmos complexos via Dapper calculam a vigência temporal cruzando a versão original com todos os aditivos, permitindo que lançamentos retroativos sejam validados contra a realidade do contrato naquela data específica.
* Funcionalidade de **Rollback seguro**, permitindo cancelar o último aditivo e restaurar a árvore orçamentária exatamente como era antes.

### 3. Segurança Granular (RBAC) e Auditoria Extrema
* **Permissões Cirúrgicas:** Controle de acesso baseado em papéis tão detalhado que permite gerenciar visualizações, edições e exclusões em nível de *ação* e até de *campo* (ex: apenas usuários com perfil 'Gestor' podem realizar lançamentos com datas retroativas).
* **Log Absoluto:** Todo registro de "Quem, Quando e O Que" foi alterado é salvo, exigindo justificativas em banco para estornos ou cancelamentos de aditivos.
* **Multi-tenant Real:** Filtros globais nos repositórios garantem o isolamento absoluto (Row-Level) dos dados financeiros entre diferentes unidades hospitalares.

### 4. Alta Performance com Micro-ORM
A camada de persistência foi escrita em **Dapper**, abandonando o overhead do Entity Framework tradicional. O uso de `QueryMultipleAsync`, rotinas recursivas em SQL e Transações em cascata (`TransactionScope`) garante que relatórios de BI pesado e geração de árvores orçamentárias sejam renderizados em milissegundos.

## 🖥️ UX e Relatórios Gerenciais
* **Árvore de Orçamentos Dinâmica:** Interface complexa em Vanilla JS/jQuery para criação infinita de subníveis orçamentários com cálculo matemático de "Subtotais" e "Provisão Mensal" em tempo real.
* **Alertas Inteligentes:** Integração fluida com SweetAlert2 para notificações transacionais.
* **Relatórios Nativos:** Geração de PDFs de alta fidelidade a partir de Views HTML (usando biblioteca Rotativa/GDI+ Windows Server), permitindo que a diretoria emita auditorias completas com 1 clique.