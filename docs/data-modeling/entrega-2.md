# Entrega 2 | Data Modeling

Sobre o escopo da Modelagem de Dados:
Na arquitetura desenhada na Entrega 1, foram mapeados 9 componentes. No entanto, a modelagem lógica a seguir foca estritamente nos 3 sistemas de persistência (armazenamento) da nossa arquitetura: Banco Operacional (OLTP hospedado no Supabase), Diretório de Armazenamento e Data Warehouse (OLAP). Os demais sistemas levantados (App, Painel Web, API, ETL, BI e Mapas) não possuem modelo de dados próprio, atuando apenas como produtores, transportadores ou consumidores dos dados estruturados nestes três repositórios principais.

## Modelagem do Banco Operacional (OLTP)

Foco na terceira forma normal (3FN) para garantir integridade e velocidade nas transações diárias (CRUD).

<img width="787" height="749" alt="1" src="https://github.com/user-attachments/assets/c4a0665d-19da-4770-be20-3d42d64c813b" />

## Modelagem do Diretório de Armazenamento e Auditoria

Diretório de logs (NoSQL) apartado para conformidade com a LGPD (registro imutável de acessos).

<img width="629" height="573" alt="logAuditoria" src="https://github.com/user-attachments/assets/67bf22d8-1b80-4582-a65e-d6c74d48afb6" />

## Modelagem do Data Warehouse (Analítico/BI)

Utiliza o Modelo Dimensional (Esquema Estrela), otimizado para agregações e painéis.

<img width="743" height="584" alt="datawarehouse" src="https://github.com/user-attachments/assets/21973329-5659-4093-a6f8-3dc3ac2b133f" />

## Alinhamento DW

* **Quais dados vão para o Data Warehouse?**  
  Vão apenas dados estruturados e consolidados para análise (nomes de bairros em vez de ruas exatas, faixas de horários, contagem de passageiros, identificação da empresa e da rota).

* **Como eles saem do banco operacional?**  
  Diariamente, o processo de ETL extrai os dados do OLTP usando colunas de `data_ultima_modificacao`. Esses dados vão para a Staging Area, onde os dados sensíveis (como atributos pessoais identificáveis) são pseudonimizados ou descartados.

* **Qual é a finalidade?**  
  Permitir ao Sistema de BI gerar painéis para a Diretoria e RH sobre ocupação da frota, pontualidade, demandas por região e custos, totalmente isolado da operação transacional e sem expor dados pessoais diretos.
