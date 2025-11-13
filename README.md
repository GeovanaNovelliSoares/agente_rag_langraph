# 🤖 Agente RAG para Políticas Internas (LangChain + LangGraph + Gemini)

## Visão Geral do Projeto

Este projeto demonstra a implementação de um Agente de Respostas e Triage de Alto Desempenho usando a arquitetura **Retrieval-Augmented Generation (RAG)**. O agente é projetado para processar e responder a consultas de usuários sobre políticas internas (`.pdf`) de forma **fidedigna** (baseada em citações) e **inteligente** (com triagem de intenção).

A orquestração do fluxo de trabalho complexo é gerenciada por um **Flow State Machine (FSM)** construído com **LangGraph**.

## 🚀 Funcionalidades Principais

O agente executa uma sequência de decisões e ações para cada consulta:

1.  **Triagem de Intento (Triage):**
      * A consulta é classificada em uma das três categorias usando o LLM:
          * `AUTO_RESOLVE` (Perguntas Frequentes resolvíveis pelo RAG).
          * `OPEN_TICKET` (Solicitações complexas ou de alta urgência, ex: exclusão de dados).
          * `REQUEST_INFO` (Perguntas que exigem mais contexto do usuário).
2.  **RAG Factual:**
      * Para consultas `AUTO_RESOLVE`, o sistema busca nos documentos de política indexados.
      * Gera a resposta final e, crucialmente, inclui **citações diretas** das fontes (`refund-policies.pdf`, `access-policies.pdf`), garantindo a precisão.
3.  **Escalonamento Automático:**
      * Para consultas `OPEN_TICKET` (e.g., pedidos de exclusão de dados), o agente executa uma ação final simulada de abertura de chamado com a urgência correta (`HIGH`).

## ⚙️ Stack Tecnológico

| Componente | Tecnologia | Função |
| :--- | :--- | :--- |
| **Orquestração** | `LangGraph` | Define e gerencia o Fluxo de Máquina de Estados (FSM) do agente. |
| **Framework LLM** | `LangChain` | Base (Core) para bindings, RAG e gestão de componentes. |
| **Modelo Base (LLM)** | `Gemini 2.5 Flash` | Usado para Triagem, Geração de Resposta e Fluxos de Controle. |
| **Vector Store** | (Implementação local, ex: Chroma ou FAISS) | Armazenamento e busca semântica dos *chunks* dos PDFs. |
| **Linguagem** | `Python` | Linguagem de implementação principal. |


## 🛠️ Como Executar

### Pré-requisitos

  * Python (3.9+)
  * Chave de API do Gemini (configurada como variável de ambiente)

### 1\. Instalação de Dependências

```bash
pip install langchain langchain-core langchain-google-genai langgraph pydantic pyyaml tenacity ... 
```

### 2\. Configuração do Ambiente

Defina sua chave de API do Google Gemini:

```bash
export GEMINI_API_KEY="SUA_CHAVE_AQUI"
```

### 3\. Execução

O projeto é projetado para ser executado em um ambiente **Jupyter Notebook**.

1.  Abra o arquivo `agente_rag_langgraph.ipynb`.
2.  Execute as células em ordem, que incluirão:
      * Carregamento e indexação dos arquivos PDF na Vector Store.
      * Definição do grafo LangGraph.
      * Execução de testes de consulta (Triagem, RAG, Escalonamento).

## 💡 Demonstrações de Consultas (Saídas do Notebook)

| Consulta | Decisão | Resposta |
| :--- | :--- | :--- |
| "Posso obter um reembolso por cursos pagos adquiridos no site?" | `AUTO_RESOLVE` | Resposta fidedigna baseada em `refund-policies.pdf`. |
| "Eu preciso me registrar para acessar cursos gratuitos?" | `AUTO_RESOLVE` | Resposta fidedigna baseada em `access-policies.pdf`. |
| "Eu solicito a exclusão de todos os meus dados..." | `OPEN\_TICKET` | "Abrindo um chamado com urgência **HIGH**." |
| "Quantas pessoas no mundo estão interessadas em tópicos..." | `REQUEST\_INFO` | "Por favor, forneça mais informações sobre políticas internas." (Decisão correta: não é política interna). |

-----
