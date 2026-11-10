# Entrega 5 — Data Storage: Minha Rota

## Armazenamento
Os dados gerados pelo Minha Rota serão distribuídos em tecnologias específicas para suportar diferentes cargas de trabalho.

### 1. Banco Operacional (OLTP)
* **Qual tecnologia será utilizada?** PostgreSQL hospedado no Supabase, com manipulação de dados via ORM Prisma (e extensão PostGIS).
* **Por que essa tecnologia foi escolhida?** Por ser um banco relacional robusto e open-source, ideal para manter a integridade referencial dos dados financeiros e cadastrais (ACID). A extensão PostGIS oferece suporte nativo avançado a dados espaciais (geolocalização, coordenadas de pontos de apanha e rotas).
* **Quais dados serão armazenados?** Cadastros (Empresas, Colaboradores, Motoristas, Veículos) e a operação diária (Rotas, Paradas e o status de cada Viagem).
* **Como os dados serão organizados?** Altamente normalizados em tabelas relacionais para evitar redundâncias e garantir atualizações rápidas.
* **Qual será o volume esperado de dados?** Volume inicial moderado, com alto número de transações curtas (leitura e escrita diária do status dos passageiros).
* **Como o sistema poderá crescer?** O crescimento poderá ser gerido através de escalonamento vertical (aumentar os recursos do servidor) e particionamento das tabelas de "Viagem" por semestre ou ano para otimizar as consultas.

### 2. Diretório de Armazenamento e Auditoria
* **Qual tecnologia será utilizada?** MongoDB (NoSQL) e armazenamento isolado de logs.
* **Por que essa tecnologia foi escolhida?** Por conta da flexibilidade de uma base orientada a documentos, excelente para armazenar estruturas JSON variáveis ("detalhe_mudanca"), pois o log da alteração de um nome tem campos diferentes do log da alteração de uma rota.
* **Quais dados serão armazenados?** Trilhas de auditoria exigidas pela LGPD (quem acedeu, quem alterou, horário e o que foi modificado).
* **Como os dados serão organizados?** Orientado a eventos, no modelo "append-only" (apenas inserções, as edições são proibidas para garantir a imutabilidade).
* **Qual será o volume esperado de dados?** Volume altíssimo e de crescimento muito acelerado. Como a LGPD exige rastreabilidade, cada mínima ação de cada usuário (uma simples visualização de endereço) gera um novo registro (log) que não pode ser apagado, avolumando o banco rapidamente.
* **Como o sistema poderá crescer?** Como é um log cumulativo que cresce muito rápido, o MongoDB escala facilmente de forma horizontal (Sharding).

### 3. Data Warehouse (OLAP)
* **Qual tecnologia será utilizada?** Google BigQuery.
* **Por que essa tecnologia foi escolhida?** Por ser uma solução "serverless" (sem gestão de servidores) construída especificamente para análises em massa (Big Data) e criação rápida de painéis de BI, cobrando apenas pelas consultas realizadas.
* **Quais dados serão armazenados?** Históricos consolidados e anonimizados, métricas de pontualidade, custos e taxas de ocupação de viaturas.
* **Como os dados serão organizados?** Utilizando o Modelo Dimensional (Esquema Estrela), dividindo-se entre Tabelas de Fato (que guardam as métricas matemáticas) e Tabelas de Dimensão (que guardam o contexto para os filtros do BI).
* **Qual será o volume esperado e como cresce?** Volume muito alto, já que irá armazenar o histórico de todos os anos de operação. O crescimento é automático e transparente, gerido pelo fornecedor da cloud.

## Persistência
* **Quais dados precisam ser persistidos?** Todos os dados estruturais de negócio: Cadastros de usuários/empresas, rotas efetuadas, dados de faturação, registos de consentimento da LGPD e as trilhas de auditoria.
* **Quais dados podem ser temporários?** Sessões de utilizadores na web/app (Tokens JWT), cache de relatórios recentes (Redis) e a localização exata do autocarro enviada de forma assíncrona via GPS durante a viagem.
* **Como os dados serão inseridos, atualizados e excluídos?** Através da API backend (Minha Rota API). As exclusões serão preferencialmente "Lógicas" (soft-delete), ou seja, um campo `status='inativo'` em vez de apagar do banco, para preservar a história das rotas passadas. O apagamento definitivo (Hard-delete) acontecerá apenas em dados sensíveis quando o utilizador invocar o seu direito ao esquecimento (LGPD).
* **Como garantir a integridade dos dados?** Utilizando as "constraints" (restrições) do banco de dados (Primary Keys, Foreign Keys) e transações atômicas (se o registro de uma viagem falhar, o check-in do passageiro é revertido automaticamente no banco).
* **Como evitar perda ou duplicidade?** Evita-se a duplicidade criando "Unique Constraints" (ex: impossível dois colaboradores com o mesmo CPF ou Email).

## Backup e Recuperação
* **Como os dados serão armazenados em backup?** Serão guardados em soluções de Cloud Storage de forma encriptada, com redundância geográfica (Multi-AZ) para proteção contra desastres locais.
* **Frequência:** Backups incrementais contínuos a cada 1 a 4 horas (Point-In-Time Recovery) e backups completos (Full Backup) executados diariamente de madrugada (período de baixo tráfego).
* **Onde os backups serão armazenados?** Em um bucket de armazenamento em nuvem (Google Cloud Storage).
* **Retenção:** Mantidos por 5 anos devido a legislações e contratos de prestação de serviços (B2B), com políticas automáticas de passagem para armazenamento a frio (mais barato) após 6 meses.
* **Recuperação:** Em caso de falha, aciona-se o restauro do último snapshot, sendo possível reverter a base de dados exata para o momento anterior à falha (PITR).

## Transacional vs Analítico
* **Quais dados permanecem no banco operacional?** Os dados "vivos" da operação de hoje e planeamento de amanhã. Tudo o que é alterado frequentemente (moradas e listas ativas).
* **Quais dados são enviados para ambientes analíticos?** Os dados frios de viagens já concluídas, mas transformados e anonimizados.
* **Como o armazenamento atende às necessidades transacionais?** O PostgreSQL (OLTP) foca a sua latência e design na rapidez de operações de escrita pontuais: um motorista lê a sua rota instantaneamente; o RH altera um turno na hora sem bloquear o sistema.
* **Como o armazenamento atende às necessidades analíticas?** O Data Warehouse (OLAP) é desenhado (Modelo Estrela com as Dimensões) para ler milhões de linhas de uma só vez (agregações) sem abrandar o banco da operação e de uma forma perfeitamente isolada.
