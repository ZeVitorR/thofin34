# Gerador de Títulos de Contas a Receber (THOFIN34)

Este componente Protheus fornece uma interface de **Browser Editável** para a gestão e inclusão manual de títulos na tabela **SE1 (Contas a Receber)**, permitindo o desmembramento de parcelas com base em documentos de saída.

## 📋 Descrição do Processo
A rotina automatiza a criação de registros financeiros a partir de uma nota fiscal de saída existente, validando as condições de pagamento e índices de correção.

1.  **Entrada de Dados**: O usuário define o tipo de título (Fixo ou Variado), documento de saída, série, quantidade de parcelas e valor através de um `ParamBox`.
2.  **Geração Temporária**: O sistema cria uma tabela em memória e projeta automaticamente os vencimentos mensais a partir da data informada.
3.  **Edição Interativa**: Através de um browser customizado, o usuário pode ajustar os campos de **Vencimento, Valor, Securitizadora e Parceiro** diretamente na grade.
4.  **Conciliação**: A rotina monitora em tempo real se o somatório das parcelas editadas coincide com o valor total do pedido original.

## 🛠️ Detalhes Técnicos

### Funções Principais
* **`THOFIN34`**: função que inicializa variáveis privadas e dispara o fluxo de criação da tabela temporária e do browser.
* **`CriaTemp`**: Instancia a classe `FWTemporaryTable` para gerenciar os dados em memória sem afetar o banco de dados antes da confirmação.
* **`GeraBrowser`**: Configura o objeto `FWMBrowse` com edição de células (`SetEditCell`) e define a interface visual com painéis de totalização utilizando fontes customizadas.
* **`realizaSalvamento`**: Processa a gravação definitiva utilizando a rotina automática `FINA040` (`MsExecAuto`) dentro de um bloco de transação segura.
* **`AtualizaValor`**: Recalcula dinamicamente os totais de entrada e parcelas exibidos na tela conforme as alterações no browser.

### Tabelas e Integrações
* **SF2 / SD2**: Utilizadas para localizar o documento de saída e identificar o produto e pedido de origem.
* **SC5 / SC6**: Consultadas para obter o índice de correção, valor total do pedido e validação de TES.
* **SE1**: Destino final dos títulos gerados via integração automática.
* **ZZZ**: Tabela customizada atualizada para manter o histórico de propriedade e vinculação do título ao cliente.

## ⚠️ Regras de Negócio Implementadas
* **Consistência de Valores**: O sistema impede o salvamento se o valor total cadastrado divergir do valor total do pedido.
* **Validação de Existência**: Verifica se o título (Prefixo + Número + Parcela) já existe na **SE1** antes de tentar a inclusão para evitar duplicidade.
* **Controle Transacional**: Utiliza `Begin Transaction` e `DisarmTransaction` para garantir a integridade dos dados em caso de erro no processo automático.
