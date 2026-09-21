# Vértice Retail: Analytics Integrado & Classificador de Atendimento IA 🚀

Este repositório contém o ecossistema completo de análise de dados e inteligência artificial desenvolvido para a **Vértice Retail**. O projeto abrange desde o diagnóstico financeiro e operacional até a implementação de uma Prova de Conceito (PoC) para triagem automatizada de chamados utilizando LLMs com saída estruturada.

---

## 📂 Estrutura do Repositório

O projeto está dividido em três módulos principais:

### 1. Diagnóstico Operacional e Financeiro (`Análise_Geral_VR.ipynb`)
Análise 360º que consolida dados de Vendas, Clientes, Marketing, Estoque e Atendimento.
*   **Destaques Técnicos:** 
    *   Algoritmo de rateio diário proporcional para investimentos de marketing.
    *   Conciliação de Faturamento Bruto vs. Efetivo (líquido de devoluções).
    *   Avaliação de Capital Imobilizado vs. Capital em Trânsito no estoque.
    *   Tratamento de escala para dados de simulação (*Mock Data*).

### 2. Analytics de Customer Experience (`Analise_Completa_Atendimento.ipynb`)
Análise profunda da eficiência do suporte e satisfação do cliente.
*   **Destaques Técnicos:**
    *   **Anti-Corruption Parsing:** Algoritmo customizado para tratar CSVs com vírgulas inseridas por usuários em campos de texto.
    *   Cálculo de métricas de SLA (Tempo de Primeira Resposta) e Tempo de Resolução.
    *   Correlação entre canais de entrada, custo por ticket e nota de CSAT.

### 3. Classificador de Atendimento Inteligente (`PoC_Classificador_de_Atendimento.ipynb`)
Prova de Conceito de um motor de triagem baseado em IA Generativa.
*   **Destaques Técnicos:**
    *   **Structured Output:** Uso de **Pydantic** para garantir 100% de conformidade do JSON de saída.
    *   **Chain of Thought (CoT):** Implementação de campo de raciocínio obrigatório, forçando a IA a justificar a classificação antes de executá-la, reduzindo drasticamente as alucinações.
    *   **Business Logic Enforcement:** Regras críticas para detecção de extravios (palavras-chave como "sumiu" ou "roubado") e roteamento automático para fluxos de autoatendimento (WISMO).
    *   **Performance:** Redução do SLA de triagem de **>2 horas** (manual) para **<2 segundos** (IA).

---

## 🛠️ Stack Tecnológica

*   **Linguagem:** Python 3.10+
*   **Manipulação de Dados:** Pandas, NumPy
*   **IA & LLM:** LangChain, LiteLLM (compatível com GPT-4o, Claude, Gemini)
*   **Validação de Esquema:** Pydantic
*   **Visualização:** Seaborn, Matplotlib

---

## 🚀 Como Executar

1.  Clone o repositório:
    ```bash
    git clone https://github.com/vianjus/vertice-retail-analytics.git
    ```
2.  Instale as dependências:
    ```bash
    pip install -r requirements.txt
    ```
3.  Configure suas chaves de API (se necessário para o notebook da PoC) no ambiente.
4.  Execute os notebooks seguindo a ordem sugerida na estrutura.

---

## 📈 Impacto de Negócio

*   **Eficiência:** Desvio estimado de 30-40% dos tickets simples (rastreio) para automação.
*   **Retenção:** Identificação imediata de clientes em risco de churn (sentimento "Furioso").
*   **Visibilidade:** Dashboard financeiro com dados de marketing rateados por dia real de campanha.

---

## 👤 Autor

**Vinícius Nunes**  
*Estudante de Ciência da Computação*  
Focado Dados, IA.

---
*Este projeto foi desenvolvido como uma solução técnica para otimização de operações de E-commerce e Atendimento ao Cliente.*
*Projeto não oficial e com intuito de estudo para Bootcamp da Elogroup*
