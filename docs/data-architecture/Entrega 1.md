# Entrega 1 | Data Architecture

## Sistemas
A arquitetura é composta por nove componentes: duas aplicações de uso (App Móvel e Painel Web, desenvolvidos em React.js), uma API hospedada em um VPS (AWS EC2) que concentra as regras de negócio utilizando o ORM Prisma, um banco transacional (Supabase), um diretório de armazenamento, um serviço externo de mapas (Google Maps API) e a camada analítica (ETL, Data Warehouse e BI).

| # | Sistema | Tipo | Quem usa | Responsabilidade |
| :--- | :--- | :--- | :--- | :--- |
| **1** | App Móvel (PWA) | Aplicação (front) | Motorista (e colaborador, em consulta restrita) | Exibir veículo, horários, rota, paradas e nomes dos colaboradores do dia |
| **2** | Painel Web Administrativo | Aplicação (front) | ADM/Transportadora e RH | Cadastros, associações, criação de rotas, aprovação/sugestão de embarques |
| **3** | API Backend | Serviço de negócio | Painel Web e App | Regras de negócio, autenticação, controle de acesso por perfil, gravação de auditoria |
| **4** | Banco Operacional (OLTP) | Banco relacional transacional | API Backend | Guardar o estado atual da operação (cadastros, rotas, viagens) |
| **5** | Diretório de Armazenamento e Auditoria | Diretório NoSQL apartado | API Backend | Usuários, perfis, consentimentos e trilha de auditoria |
| **6** | Serviço de Mapas/Geolocalização (Google Maps API) | Serviço externo | API Backend | Validar endereços e calcular trajetos |
| **7** | ETL + Staging | Pipeline de dados | Equipe de Dados | Extrair do operacional, tratar, anonimizar e carregar no DW |
| **8** | Data Warehouse (DW) | Banco analítico | Camada de BI | Histórico consolidado e otimizado para consulta |
| **9** | Sistema de BI | Dashboards | Diretoria, ADM, RH | Indicadores de ocupação, pontualidade, demanda e conformidade |

## Desenho da arquitetura

<img width="1804" height="1193" alt="desenhoarquitetura" src="https://github.com/user-attachments/assets/ebbb56c8-9da1-4d82-b77e-ea92a04fa680" />
Figura 1 — Data Architecture do Minha Rota (transacional em verde, analítico em roxo).

## Fluxo dos dados

### Fluxo transacional (dia a dia)
1. O ADM, pelo Painel Web, cadastra colaborador, empresa, veículo e motorista; a API valida e grava no Banco Operacional.
2. O ADM associa veículo a motorista e cria a rota; a API usa o serviço de Mapas (Google Maps API) para validar os endereços das paradas e calcular o trajeto.
3. O RH sugere a inclusão de um colaborador; a sugestão fica pendente no Banco Operacional até a aprovação. Aprovada, o ADM associa o colaborador a uma parada da rota.
4. O motorista abre o App; a API devolve apenas veículo, horários, paradas e nomes dos colaboradores da rota dele (cópia temporária no celular).
5. Toda inclusão, alteração, exclusão e consulta sensível gera um registro no Diretório de Armazenamento e Auditoria.

### Fluxo analítico (carga periódica)
1. Todo dia de madrugada o ETL lê do Banco Operacional só o que mudou (coluna de última atualização), sem pesar a operação.
2. Os dados caem em tabelas de Staging, onde são limpos, padronizados e pseudonimizados (sem nome nem endereço exato).
3. O ETL carrega o DW (dimensões e fatos).
4. O BI consulta apenas o DW, nunca o banco operacional.

## Transacional vs Analítico

| Banco | Natureza | Responsabilidade no projeto | Padrão de uso | Retenção |
| :--- | :--- | :--- | :--- | :--- |
| **Operacional (OLTP)** | Transacional | Estado atual: quem é motorista, qual rota existe, quem está em qual rota | Muitas leituras/escritas pequenas e rápidas; dados normalizados | Enquanto o vínculo estiver ativo; descarte/anonimização no desligamento |
| **Diretório de Armazenamento** | Transacional (append-only para logs) | Quem acessa o quê; prova de conformidade com a LGPD | Escrita contínua, leitura para investigação | Conforme política de segurança (prazo maior que o dado operacional) |
| **Staging** | Transitório | Área de trabalho do ETL | Carga em lote | Apagado após cada carga bem-sucedida |
| **Data Warehouse** | Analítico | Histórico para análise: ocupação, pontualidade, demanda por bairro/empresa | Poucas consultas grandes, só leitura; modelo estrela | Longo prazo, sem dados pessoais diretos |
