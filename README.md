# Análise de Vendas usando SQL e EXCEL

## Contexto

Este projeto foi desenvolvido para analisar os dados de vendas de um supermercado. A análise visa entender o desempenho das filiais, as preferências dos clientes e as tendências de vendas ao longo do tempo. Com as informações obtidas, será possível tomar decisões mais informadas e estratégicas para melhorar as operações e aumentar a rentabilidade.

## Objetivo do Projeto

O objetivo principal deste projeto é realizar uma análise detalhada das vendas do supermercado, focando nas seguintes questões:

## Questões de Negócios

1. **Qual é a média de vendas por filial?**
2. **Qual é o total de produtos vendidos por filial?**
3. **Qual é o total de vendas por tipo de cliente?**
4. **Qual é o total de vendas por classificação (Rating)?**
5. **Qual é o total de imposto pago por filial?**

## Código SQL

O código abaixo foi desenvolvido para criar uma tabela de produção com base nas questões de negócios mencionadas:

```sql
-- 1. Média de vendas por filial
WITH Average_Sales AS (
    SELECT 
        Branch, 
        MONTH(CONVERT(DATE, [Date], 101)) AS Sales_Month,  -- Extrair o mês
        DAY(CONVERT(DATE, [Date], 101)) AS Sales_Day,      -- Extrair o dia
        AVG(CAST(Sales AS FLOAT)) AS Average_Sales
    FROM 
        [SuperMarket Analysis]
    GROUP BY 
        Branch, 
        MONTH(CONVERT(DATE, [Date], 101)), 
        DAY(CONVERT(DATE, [Date], 101))  -- Agrupar por mês e dia
),

-- 2. Total de produtos vendidos por filial
Total_Products_Sold AS (
    SELECT 
        Branch,
        MONTH(CONVERT(DATE, [Date], 101)) AS Sales_Month,
        DAY(CONVERT(DATE, [Date], 101)) AS Sales_Day,
        COUNT(*) AS Total_Sold
    FROM 
        [SuperMarket Analysis]
    GROUP BY 
        Branch,
        MONTH(CONVERT(DATE, [Date], 101)),
        DAY(CONVERT(DATE, [Date], 101))
),

-- 3. Total de vendas por tipo de cliente
Total_Sales_By_Customer_Type AS (
    SELECT 
        Branch,
        MONTH(CONVERT(DATE, [Date], 101)) AS Sales_Month,
        DAY(CONVERT(DATE, [Date], 101)) AS Sales_Day,
        SUM(CAST(Sales AS FLOAT)) AS Total_Sales
    FROM 
        [SuperMarket Analysis]
    GROUP BY 
        Branch, 
        [Customer type],
        MONTH(CONVERT(DATE, [Date], 101)),
        DAY(CONVERT(DATE, [Date], 101))
),

-- 4. Total de vendas por classificação (Rating)
Total_Sales_By_Rating AS (
    SELECT 
        Branch,
        MONTH(CONVERT(DATE, [Date], 101)) AS Sales_Month,
        DAY(CONVERT(DATE, [Date], 101)) AS Sales_Day,
        SUM(CAST(Sales AS FLOAT)) AS Total_Sales
    FROM 
        [SuperMarket Analysis]
    GROUP BY 
        Branch, 
        Rating,
        MONTH(CONVERT(DATE, [Date], 101)),
        DAY(CONVERT(DATE, [Date], 101))
),

-- 5. Total de imposto pago por filial
Total_Tax_By_Branch AS (
    SELECT 
        Branch,
        MONTH(CONVERT(DATE, [Date], 101)) AS Sales_Month,
        DAY(CONVERT(DATE, [Date], 101)) AS Sales_Day,
        SUM(CAST([Tax 5%] AS FLOAT)) AS Total_Tax
    FROM 
        [SuperMarket Analysis]
    GROUP BY 
        Branch, 
        MONTH(CONVERT(DATE, [Date], 101)),
        DAY(CONVERT(DATE, [Date], 101))
)

-- Combina todos os resultados em uma tabela final
SELECT 
    A.Branch,
    A.Sales_Month,
    A.Sales_Day,
    A.Average_Sales,
    T.Total_Sold,
    C.Total_Sales AS Customer_Type_Sales,
    R.Total_Sales AS Rating_Sales,
    B.Total_Tax
FROM 
    Average_Sales A
LEFT JOIN 
    Total_Products_Sold T ON A.Branch = T.Branch AND A.Sales_Month = T.Sales_Month AND A.Sales_Day = T.Sales_Day
LEFT JOIN 
    Total_Sales_By_Customer_Type C ON A.Branch = C.Branch AND A.Sales_Month = C.Sales_Month AND A.Sales_Day = C.Sales_Day
LEFT JOIN 
    Total_Sales_By_Rating R ON A.Branch = R.Branch AND A.Sales_Month = R.Sales_Month AND A.Sales_Day = R.Sales_Day
LEFT JOIN 
    Total_Tax_By_Branch B ON A.Branch = B.Branch AND A.Sales_Month = B.Sales_Month AND A.Sales_Day = B.Sales_Day;
```

## Etapas do Projeto

1. **Carregamento da Planilha no Excel**
   - A planilha `[SuperMarket Analysis]` foi importada para o Excel, garantindo que todos os dados estavam corretamente formatados.

2. **Criação da Tabela Dinâmica**
   - Foi criada uma tabela dinâmica no Excel utilizando os dados obtidos a partir do código SQL.
   - As colunas foram organizadas de acordo com as questões de negócios para permitir uma análise mais fácil.

3. **Visualização de Dados com o Dashboard**
   - Com base nos dados da tabela dinâmica, gráficos foram criados para visualizar as métricas importantes.
   - Os gráficos foram organizados em um dashboard, que permitiu uma visão clara das vendas por filial, tipo de cliente e outras métricas relevantes.
     
![Dashboard](https://github.com/user-attachments/assets/bdb28a5a-ccfb-4a94-9c2f-dba7b47351f9)

Analisando o dashboard, aqui estão alguns insights:

1. **Média de Vendas por Tipos de Clientes**:
   - O gráfico de pizza mostra que as vendas estão distribuídas de forma equilibrada entre os diferentes tipos de clientes, com uma variação muito pequena nas porcentagens (entre 17,38% e 18,50%). Isso sugere que nenhum tipo de cliente domina significativamente as vendas.

2. **Média de Venda por Filial**:
   - A filial **Giza** tem a maior média de venda, superando **Cairo** e **Alex**, com uma diferença notável. Isso indica que **Giza** está se destacando em termos de ticket médio por venda.
   - As filiais **Alex** e **Cairo** estão próximas, mas **Cairo** tem uma média de venda um pouco maior que **Alex**.

3. **Total de Impostos Pagos por Filial**:
   - Aqui, podemos ver que a filial **Giza** também lidera no total de impostos pagos, o que é coerente com sua maior média de vendas. Isso sugere que **Giza** está gerando mais receita e, portanto, pagando mais impostos.
   - As filiais **Alex** e **Cairo** têm impostos pagos praticamente iguais, refletindo sua menor performance de vendas.

4. **Média de Classificação dos Produtos por Filial**:
   - A filial **Giza** também lidera na média de classificação dos produtos, o que pode indicar maior satisfação dos clientes nessa unidade.
   - **Alex** e **Cairo** têm classificações similares, mas ainda abaixo de **Giza**.

5. **Total de Produtos Vendidos por Filial**:
   - Em termos de quantidade de produtos vendidos, a filial **Alex** lidera, seguida por **Cairo** e **Giza**. Isso é interessante, pois **Giza** tem a maior média de venda, mas vende menos produtos em volume. Isso pode indicar que **Giza** está vendendo itens de maior valor, enquanto **Alex** tem um volume maior de produtos, mas com ticket médio menor.

### Conclusão

Esses insights podem ser usados para direcionar estratégias de negócios, como focar em treinamento de funcionários na filial com menor desempenho de vendas ou realizar campanhas promocionais específicas para aumentar a média de vendas em filiais com desempenho inferior.


