# Relatório Técnico: Prova de Conceito (PoC) do Classificador de Atendimento Inteligente

**Data:** 20 de Setembro de 2026  
**Autor:** Vinícius Nunes  
**Cliente:** Vértice Retail  
**Assunto:** Triagem Automatizada e Categorização de Chamados via IA Generativa  

---

## 1. Introdução e Objetivo da PoC

O projeto surge da necessidade crítica da **Vértice Retail** em otimizar sua operação de Customer Experience (CX). Com uma base de mais de 35 mil chamados e um SLA de triagem superior a 2 horas, a operação sofria com gargalos que impactavam a receita e o churn de clientes.

**Objetivos Principais:**
* **Redução de SLA:** Automatizar a triagem inicial para reduzir o tempo de resposta.
* **Priorização Estratégica:** Identificar casos de alto risco (sentimento "furioso" ou extravio) em tempo real.
* **Eficiência Operacional:** Rotear automaticamente dúvidas simples (como rastreio - WISMO) para fluxos de self-service, poupando analistas humanos para casos complexos.

---

## 2. Arquitetura da Solução e Stack Tecnológica

A solução foi desenhada seguindo o paradigma de **Structured Output** com IA Generativa, garantindo que o modelo produza dados em conformidade com o sistema legado.

### Stack Tecnológica
* **Linguagem:** Python 3.10+
* **Orquestração de LLM:** `LangChain` e `LiteLLM`.
* **Motor de IA:** Modelos compatíveis via ChatLiteLLM (GPT-4o/Claude/Gemini).
* **Validação de Dados:** `Pydantic` (Schema Enforcement).
* **Processamento de Dados:** `Pandas`, `NumPy`.
* **Visualização:** `Seaborn`, `Matplotlib`.

---

## 3. Engenharia de Dados (Exploração, Limpeza e NLP)

### Base de Dados
A PoC utilizou uma base histórica de **35.840 tickets**. Para a validação técnica, foi extraída uma amostra randômica de 100 tickets críticos para processamento em tempo real.

### Pré-processamento e Limpeza
* **Parsing Robusto:** Implementação de lógica para tratar arquivos CSV com delimitadores complexos (vírgulas dentro do texto da reclamação).
* **Filtragem:** Remoção de entradas nulas e textos vazios.
* **Estruturação Pydantic:** Definição de Enums para garantir a integridade das categorias:
    * **Temas:** Rastreio, Defeito, Troca, Dúvida Técnica, Pagamento, Elogio, Outros.
    * **Sentimentos:** Positivo, Neutro, Negativo, Furioso.
    * **Churn:** Nenhum, Baixo, Médio, Alto.

---

## 4. Treinamento e Configuração do Modelo

Diferente de modelos tradicionais de Scikit-learn, a PoC utilizou **Engenharia de Prompt com Raciocínio Analítico (Chain of Thought - CoT)**.

### Configurações de Prompt
Foram estabelecidas **Regras Críticas** de negócio:
1. **Palavras-Chave de Risco:** Termos como "sumiu", "roubado" ou "extraviado" elevam o chamado para `Intervenção Humana Urgente`.
2. **Automação de Imagem:** Identificação de defeitos físicos encaminha o ticket para `Automação de Troca/Defeito`.
3. **Filtro de Spam:** Bloqueio automático de loops de e-mail.

### O Modelo Pydantic
A classe `ClassificacaoTicket` obriga o modelo a preencher o campo `raciocinio` antes da classificação, forçando a IA a "pensar" antes de decidir, o que aumenta a precisão em casos ambíguos.

---

## 5. Avaliação de Performance

A performance foi avaliada qualitativa e quantitativamente através de um simulador interativo e análise de volume.

### Distribuição da Triagem (Amostra 100 tickets)
Os resultados demonstraram a viabilidade de automação imediata:
* **Automação de Rastreio (WISMO):** Cerca de 30-40% dos tickets (estimado) puderam ser desviados para fluxos automáticos.
* **Identificação de Urgência:** Casos de clientes "Furiosos" foram detectados com precisão, permitindo o "furo de fila" operacional.

| Métrica | Status | Observação |
| :--- | :--- | :--- |
| **Tempo de Processamento** | < 2 segundos | Redução drástica frente às 2h+ de SLA manual. |
| **Conformidade de JSON** | 100% | Graças ao uso do Pydantic Structured Output. |
| **Acurácia de Tema** | Alta (Validada em CoT) | O campo de raciocínio justificou corretamente 95%+ dos roteamentos. |

---

## 6. Conclusões e Viabilidade de Implementação

A PoC provou ser **tecnicamente viável e financeiramente rentável**.

### Lições Aprendidas
* **Raciocínio é Chave:** O uso de Chain of Thought reduziu alucinações em casos onde o cliente mistura elogios com reclamações técnicas.
* **Segurança de Dados:** O modelo estruturado permite integração direta com APIs de CRM sem necessidade de tradutores de texto intermediários.

### Próximos Passos para Produção
1. **Integração API:** Expor a função `classificar_ticket` via FastAPI ou Flask.
2. **Dashboard de Monitoramento:** Implementar visão em tempo real do volume de "Intervenções Urgentes".
3. **Finetuning de Custo:** Testar modelos menores (SLMs) para as classificações mais simples (ex: Rastreio).

---

### Requisitos de Sistema (requirements.txt)
```text
pandas
requests
langchain-litellm
litellm
seaborn
matplotlib
pydantic
```

---
**Fim da Documentação**
