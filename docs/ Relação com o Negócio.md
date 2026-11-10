# Entrega 9 — Relação com o Negócio

## A. Data Storage

A estratégia de armazenamento separa o ambiente transacional do analítico. O Banco Operacional (OLTP) garante que as consultas e cadastros do dia a dia da transportadora e do RH ocorram com máxima velocidade e sem gargalos. O Data Warehouse, por sua vez, armazena o histórico consolidado de forma isolada, permitindo que a diretoria analite tendências sem impactar a operação diária.

## B. Data Security

A segurança protege o negócio contra vazamentos de dados e multas severas decorrentes da LGPD. Ao isolar a trilha de auditoria (LOG_AUDITORIA), aplicar controle rígido de acesso por perfis na API e pseudonimizar os dados no DW (removendo nomes e endereços exatos), a empresa resguarda a privacidade dos colaboradores e protege sua própria reputação institucional.

## C. Data Integration

A integração elimina o uso caótico de planilhas descentralizadas e fluxos em papel desorganizados. A comunicação automatizada entre o Painel Web, a API, o aplicativo móvel do motorista e o sistema de BI garante que todas as áreas (RH, Operações e Gestão) visualizem a mesma informação através de sincronização assíncrona, reduzindo erros de comunicação e atrasos nas rotas.

## D. Data Architecture + Data Modeling

As escolhas de arquitetura e modelagem trazem impactos diretos e mensuráveis para o negócio:

- **Dados mais organizados e auditáveis:** Padronização estrutural rigorosa por meio da 3FN no operacional e do Esquema Estrela no DW, e imutabilidade no diretório NoSQL de auditoria.
- **Redução de inconsistências:** Validações automáticas de chaves e restrições na API impedem a inserção de dados duplicados ou órfãos.
- **Melhora na tomada de decisão:** O sistema de BI gera indicadores precisos (taxa de ocupação de frotas, pontualidade e custos por rota) para embasar decisões estratégicas da diretoria.
- **Aumento da segurança:** Separação estrita de camadas e anonimização analítica garantem conformidade regulatória nativa.
- **Integração de diferentes sistemas:** Arquitetura orientada a serviços (API) que conecta facilmente aplicações internas e ferramentas externas de mapas.
- **Facilidade de evolução do sistema:** Modularidade que permite adicionar novos módulos, frotas ou empresas parceiras sem reescrever a base do sistema.
- **Geração de valor para o negócio:** Transformação de dados brutos e dispersos em inteligência operacional, otimizando custos com transporte e elevando a qualidade de vida e a segurança dos colaboradores.
