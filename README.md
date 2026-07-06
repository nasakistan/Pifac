# 🧾 Processamento Inteligente de Faturas com UiPath Action Center

Um projeto de RPA de nível corporativo construído com UiPath Studio, demonstrando o processamento de documentos de ponta a ponta utilizando Inteligência Artificial (Document Understanding) e validação humana (Human-in-the-Loop via Action Center).

## 🚀 Visão Geral da Arquitetura

Este projeto está dividido em um Dispatcher e um Performer, utilizando o robusto **Robotic Enterprise Framework (REFramework)** para garantir escalabilidade, tratamento de erros e processamento transacional.

### 1. Dispatcher
- Varre um diretório local em busca de PDFs de Notas Fiscais (Faturas) de entrada.
- Adiciona cada documento de forma segura em uma Fila (Queue) do Orchestrator.

### 2. Performer (REFramework + Document Understanding)
- **Inicialização:** Conecta-se ao Orchestrator, carrega as configurações e busca o Item da Fila.
- **Digitalização e Classificação:** Usa o UiPath Document OCR para digitalizar o PDF.
- **Extração de Dados com IA:** Utiliza o `Machine Learning Extractor` (modelo pré-treinado de Faturas) para extrair dados não estruturados de forma inteligente:
  - Número da Nota
  - CNPJ do Fornecedor
  - Valor Total
- **Human-in-the-Loop (Action Center):** Avalia a porcentagem de confiança da extração da IA contra um limite (ex: 80%). Se a confiança for baixa, o robô cria uma tarefa no Orchestrator e **Suspende** a execução (liberando a licença da máquina).
- **Validação:** Um operador humano revisa e corrige os campos através da interface web do Action Center.
- **Retomada e Exportação:** Após a aprovação do humano, o robô acorda (Resume), recupera os dados validados e constrói um pacote de dados JSON.
- **Integração via API:** Dispara os dados finais e validados para um sistema externo usando uma requisição HTTP POST (via Webhook).

## 🛠️ Tecnologias Utilizadas
- UiPath Studio (Windows / VB)
- Robotic Enterprise Framework (REFramework)
- UiPath Document Understanding (ML Extractor)
- UiPath Action Center
- UiPath WebAPI (Requisições HTTP)
- Estruturação de Dados em JSON

## 💡 Principais Aprendizados
- Gerenciamento avançado do escopo de variáveis durante os estados de suspensão e retomada (resume) do robô.
- Sobrescrita de lógicas de extração de dados para mesclar de forma limpa os dados da IA com os dados validados pelo humano (usando Ifs em linha).
- Integração nativa de APIs externas (Webhooks) dentro da máquina de estados do REFramework.

## ⚙️ Como Executar
1. Atualize a aba Settings do arquivo `Config.xlsx` com o nome da sua Fila do Orchestrator, nome do Storage Bucket e configure a Chave de API na aba Assets.
2. Execute o projeto `Dispatcher.xaml` para popular a fila com PDFs de amostra.
3. Execute o `Main.xaml` para iniciar o Performer.
4. Acesse o UiPath Orchestrator > Action Center para aprovar manualmente as tarefas suspensas.
