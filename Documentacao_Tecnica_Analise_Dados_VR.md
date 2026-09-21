# 📑 Documentação Técnica: Processamento e Análise de Dados VR

**Especialista Responsável:** Vinícius Nunes  
**Data:** 21 de Setembro de 2026  
**Domínio:** Engenharia de Dados e Business Intelligence

---

## 1. Script: Análise Geral VR

### 📋 Resumo Executivo
Este script tem como objetivo realizar o **diagnóstico financeiro e operacional integrado** da Vértice Retail. Ele consolida cinco fontes de dados distintas (Vendas, Clientes, Marketing, Estoque e Atendimento) para calcular o faturamento real, margens de contribuição e saúde do inventário, aplicando correções de escala necessárias para dados de simulação (*mock data*).

### 🛠️ Dependências
*   `pandas`: Manipulação de DataFrames e séries temporais.
*   `numpy`: Operações matemáticas vetoriais e arredondamentos.
*   `os`: Navegação em diretórios e busca dinâmica de arquivos.

### 🔄 Descrição do Fluxo de Dados
1.  **Ingestão Dinâmica:** O script mapeia o diretório local e utiliza busca por padrões de texto para carregar arquivos CSV, evitando erros por nomes de arquivos variáveis.
2.  **Recalibragem de Escala (Data Wrangling):** Como o dataset original é uma simulação, o script aplica um redutor de **100x** nos valores de estoque e investimentos de marketing para alinhar a volumetria à realidade analítica.
3.  **Normalização Temporal:** Converte strings para objetos `datetime` e delimita uma janela de análise estrita (término em 26/01/2024) para evitar distorções por dados parciais.
4.  **Alocação de Custos de Marketing:** Aplica um algoritmo de rateio diário para campanhas. Se uma campanha dura 30 dias mas apenas 10 coincidem com a janela de vendas, apenas 1/3 do custo é imputado no cálculo de ROI.
5.  **Conciliação de Vendas:** Separação de Faturamento Bruto vs. Líquido, descontando devoluções e cancelamentos.

### 📖 Dicionário de Funções/Métodos Principais
*   **`carregar_csv(termo_busca)`**:
    *   *Parâmetros:* `termo_busca` (string).
    *   *Lógica:* Filtra `os.listdir()` para encontrar arquivos `.csv` que contenham o termo; levanta `FileNotFoundError` caso ausente.
    *   *Saída:* `pd.DataFrame`.
*   **`calcular_marketing_proporcional_vendas(row)`**:
    *   *Lógica:* Calcula a interseção entre o período da campanha e a janela de análise de vendas. Divide o custo total pelos dias de campanha e multiplica pelos dias de interseção.
    *   *Saída:* `float` (Valor rateado).

### 📈 Resultados Esperados
*   **Tabela de Apuração de Caixa:** Detalhamento de Faturamento Efetivo, Descontos e CMV.
*   **Visão de Inventário:** Comparativo monetário entre Capital em Trânsito (Reservado) e Capital Imobilizado (Disponível).
*   **Base Consolidada para ROI:** Dados de marketing prontos para cruzamento com vendas líquidas.

### ⚠️ Notas de Manutenção
*   **Escalabilidade:** Caso o script passe a ler dados reais de produção, as divisões por 100 nas células de carregamento devem ser removidas.
*   **Performance:** A função de rateio utiliza `.apply()`, o que é eficiente para milhares de linhas, mas pode exigir vetorização via NumPy para milhões de registros.

---

## 2. Script: Analise Completa Atendimento

### 📋 Resumo Executivo
Este script foca na **Análise de Eficiência e Qualidade de Customer Experience (CX)**. O objetivo é mensurar o desempenho da equipe de suporte, identificar canais saturados, calcular o custo por ticket e validar a satisfação do cliente (CSAT) através de uma limpeza robusta de dados não estruturados.

### 🛠️ Dependências
*   `pandas`: Limpeza e agregação de dados.
*   `requests`: Download direto da base via URL externa (Google Drive).
*   `numpy`: Apoio a cálculos de tempo médio.
*   `warnings`: Supressão de alertas de depreciação para limpeza do output.

### 🔄 Descrição do Fluxo de Dados
1.  **Ingestão Robusta (Anti-Corruption Parsing):** Implementação de um tratador de strings manual. O script identifica linhas onde o campo `texto_cliente` contém vírgulas (o que quebraria um CSV padrão) e as reconstrói dinamicamente isolando os delimitadores corretos.
2.  **Sanitização e Tipagem:**
    *   Conversão forçada de `nota_csat` e `custo_operacional` para numérico (lidando com erros de entrada).
    *   Preenchimento de valores ausentes (*NaN*) em categorias e canais como "Desconhecido".
3.  **Feature Engineering:**
    *   Criação da métrica `tempo_resolucao_horas` (diferença entre fechamento e abertura).
    *   Mapeamento de nomes de dias da semana para Português para relatórios executivos.
4.  **Agregação Estatística:** Cálculo de volume por dia, canal e categoria de problema.

### 📖 Dicionário de Funções/Métodos Principais
*   **Algoritmo de Reconstrução de Linhas (Loop Nativo):**
    *   *Lógica:* Divide a linha por vírgulas. Se o total de partes > 12, aglutina todas as partes entre o índice 8 e o antepenúltimo, tratando-as como a única coluna de texto.
*   **`pd.to_datetime(errors='coerce')`**:
    *   *Lógica:* Utilizado para garantir que datas mal formatadas não interrompam o script, transformando-as em `NaT` (Not a Time).

### 📈 Resultados Esperados
*   **KPIs de CX:** Volume total de tickets, Média de CSAT e Tempo Médio de Resolução.
*   **Gráfico de ROI Automático:** Visualização da distribuição de ações (Automação vs. Intervenção Humana).
*   **Tabela de Eficiência de Canal:** Identificação de canais com maior custo ou menor satisfação.

### ⚠️ Notas de Manutenção
*   **Dependência Externa:** O script depende da disponibilidade da URL do Google Drive (`file_id`). Recomenda-se migrar para um bucket S3 ou Azure Blob em produção.
*   **Parsing:** O algoritmo de reconstrução de linhas assume que as vírgulas extras ocorrem apenas no campo `texto_cliente`. Caso ocorram em outros campos, a lógica de índices precisará ser revisada.
