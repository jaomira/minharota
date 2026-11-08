# Entrega 3 | Modelagem fora do Digital

## Documento escolhido
A Ficha de Cadastro e Termo de Ciência do Colaborador, em papel. Ela é usada quando uma empresa parceira ainda não consegue enviar os dados pelo sistema, como contingência quando o sistema estiver fora do ar e como prova do consentimento exigido pela LGPD. O README do projeto descreve o problema que ela ajuda a controlar: hoje as listas de passageiros circulam em planilhas, sem rastreabilidade.

| Item | Definição |
| :--- | :--- |
| **Tipo de documento** | Formulário cadastral com termo de consentimento. Contém dados pessoais e a localização residencial do colaborador, o que o torna confidencial. |
| **Modelagem do documento** | Quatro blocos: cabeçalho (código da ficha, empresa, data); dados do colaborador (nome, matrícula, turno, bairro, endereço de embarque, ponto de referência); dados da rota (rota atribuída, horário previsto); consentimento (finalidade, base legal, assinatura e data). Mais um bloco de controle interno. O modelo completo está na seção seguinte. |
| **Forma de organização** | Arquivo por empresa contratante; dentro de cada empresa, por ordem alfabética do nome do colaborador; pastas suspensas com separadores por rota. |
| **Local de armazenamento** | Arquivo físico de aço com chave, em sala restrita da transportadora, longe de umidade e de luz direta. |
| **Identificação e classificação** | Etiqueta com código da ficha, empresa e rota. Classificação confidencial (dado pessoal). Índice mestre em caderno de controle para localizar qualquer ficha pelo código. |
| **Controle de acesso** | Chave sob guarda do responsável pelo arquivo; lista de pessoas autorizadas; livro de registro de retirada e devolução (quem, quando e para quê). Nenhuma ficha sai da sala sem registro. |
| **Tempo de retenção** | Enquanto o colaborador estiver vinculado à rota, mais um período curto de guarda definido pela política da empresa (sugestão: 12 meses, a validar com o jurídico). Depois, descarte. |
| **Processo de arquivamento** | Preenchimento, conferência dos campos, assinatura, digitação no sistema, carimbo "digitado", etiquetagem, guarda na pasta da rota e registro no índice mestre. |
| **Forma de recuperação** | Busca no índice mestre pelo código ou pelo nome; localização por empresa e ordem alfabética; devolução registrada no livro de controle. |
| **Riscos** | Perda ou dano (incêndio, infiltração, extravio); acesso indevido (chave copiada, ficha esquecida sobre a mesa, fotografia por celular); descarte inadequado (lixo comum com dados legíveis); desatualização (mudança de endereço sem ficha nova). |
| **Mitigações** | Armário trancado e sala restrita; extintor e local seco; cópia digital criptografada como backup; mesa limpa; trituração do papel no descarte, com registro em ata; atualização da ficha a cada mudança de endereço. |

## Como o documento é armazenado e controlado

### Modelo da ficha (campos e estrutura)
*Estrutura proposta para a ficha impressa, em uma única folha A4 frente.*

**FICHA DE CADASTRO E TERMO DE CIÊNCIA DO COLABORADOR — MINHA ROTA**

#### 1. Cabeçalho
* **Código da ficha:** FCP-2026-______
* **Empresa contratante:** ________________________________________
* **Data de preenchimento:** ____/____/________

#### 2. Dados do colaborador
* **Nome completo:** __________________________________________________
* **Matrícula:** ____________________
* **Turno:** ( ) Manhã &nbsp;&nbsp;&nbsp;&nbsp; ( ) Tarde &nbsp;&nbsp;&nbsp;&nbsp; ( ) Noite
* **Bairro:** __________________________________________________________
* **Endereço de embarque:** ____________________________________________
* **Ponto de referência:** ______________________________________________

#### 3. Dados da rota
* **Rota atribuída:** ___________________________________________________
* **Horário previsto de embarque:** ________:________

#### 4. Consentimento
> *"Autorizo o tratamento dos meus dados pessoais (nome, matrícula, turno e endereço de embarque) apenas para organizar o transporte fretado entre minha residência e a empresa, e o compartilhamento desses dados com a transportadora para essa finalidade."*

* **Assinatura do colaborador:** _______________________________________
* **Data:** ____/____/________

#### 5. Controle interno
* **Preenchido por:** ____________________
* **Conferido por:** ____________________
* **Digitado no sistema em:** ____/____/________
* **Carimbo:** `[DIGITADO]`
