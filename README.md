# Northwind-Sales-Dashboard
Dashboard analítico de vendas e logística baseado no dataset Northwind. Inclui Visão Executiva (KPIs, YTD), Análise Comercial (Performance de funcionários e elasticidade de desconto) e Visão Operacional, como processo comercial. Desenvolvido em Power BI com modelagem dimensional e DAX avançado para suporte à tomada de decisão.

O dashboard foi estruturado para responder às seguintes perguntas:

Qual é a receita total gerada, considerando os descontos aplicados?
Qual o impacto financeiro dos descontos concedidos aos clientes?
Como a receita evolui mês a mês? Estamos crescendo ou retraindo?
Qual o acumulado de receita no ano (YTD)?
Quantos clientes únicos estão ativos no período analisado?
Quantos pedidos foram realizados? Qual o ticket médio por pedido?
Quais produtos e categorias mais contribuem para a receita?
Qual o prazo médio de entrega dos pedidos?
Quantos pedidos estão pendentes de envio?
Qual o custo total e médio de frete por pedido?
Quais funcionários são responsáveis pelo maior volume de vendas?
Quais regiões ou países concentram mais receita?

Justificativa do Design Escolhido:

A principal diretriz de design foi a clareza, pois o usuário precisa entender o estado do negócio em menos de 30 segundos ao abrir o dashboard. Optei por um tom de paleta escura com destaques em tons de verde e azul. Essa escolha reduz a fadiga visual em sessões longas de análise e cria contraste natural para os KPIs em destaque. Os cartões de KPI foram posicionados no topo da página, funcionando como resumo executivo, já os gráficos de tendência e distribuição ficam na sequência, aprofundando a leitura para quem precisa do detalhe.

Páginas de tooltip foram criadas para enriquecer a experiência sem poluir o visual principal, ao passar o mouse sobre categorias, o usuário vê o frete detalhado por categoria sem sair da visão atual. A medida "Cor Top 2 Funcionários" foi criada especificamente para destacar visualmente os dois melhores vendedores, tornando o ranking imediatamente interpretável sem depender de leitura de valores. Títulos curtos e diretos, sem jargões. Espaçamento generoso entre visuais para evitar sensação de sobrecarga, especialmente em telas menores.

Explicação dos indicadores de KPIs:

Receita Total c/ Desconto: Receita líquida real, já descontando os percentuais aplicados em cada item - SUMX(order_details, [quantity] * [unitPrice] * (1 - [discount]));

Receita Total s/ Desconto: Receita bruta, sem considerar descontos, usada como base comparativa - SUMX(order_details, [quantity] * [unitPrice]);

Total Descontos: Valor total concedido em descontos, em moeda - [Receita Total s/ Desconto] - [Receita Total c/ Desconto];

% Desconto Médio Real: Percentual real de desconto sobre a receita bruta — mais preciso que a média simples - DIVIDE([Total Descontos], [Receita Total s/ Desconto]);

% Desconto Médio: Média simples do campo discount — útil para comparações rápidas por linha - AVERAGE(order_details[discount]);

Ticket Médio: Valor médio por pedido, usando a receita líquida - DIVIDE([Receita Total c/ Desconto], [Total Pedidos]);

Total Pedidos: Contagem distinta de pedidos únicos - DISTINCTCOUNT(orders[orderID]);

Total Clientes: Quantidade de clientes únicos no contexto filtrado - DISTINCTCOUNT(customers[customerID]);

Qtd Vendida: Unidades totais vendidas (soma de quantidades) - SUM(order_details[quantity]);

Qtd Produtos Vendidos: Diversidade de produtos vendidos (SKUs distintos) - DISTINCTCOUNT(order_details[productID]);

Frete Total: Custo total de frete no período - SUM(orders[freight]);

Frete Médio: Custo médio de frete por pedido - DIVIDE([Frete Total], [Total Pedidos]);

Pedidos Entregues: Pedidos com data de envio preenchida - COUNT(orders[shippedDate]);

Pedidos Pendentes: Pedidos sem data de envio — em aberto - CALCULATE(COUNT(orders[orderID]), ISBLANK(orders[shippedDate]));

Tempo Médio Entrega (dias): Média de dias entre a data do pedido e o envio, excluindo pedidos não despachados - AVERAGEX(orders, DATEDIFF(orders[orderDate], orders[shippedDate], DAY));

Receita YTD: Acumulado de receita no ano corrente, com inteligência de tempo - TOTALYTD([Receita Total c/ Desconto], d_calendario[Date]);

Receita Mês Anterior: Receita do mês imediatamente anterior ao contexto selecionado - CALCULATE([Receita Total c/ Desconto], PREVIOUSMONTH(d_calendario[Date]));

Crescimento Mensal %: Variação percentual da receita em relação ao mês anterior - DIVIDE([Receita Total c/ Desconto] - [Receita Mês Anterior], [Receita Mês Anterior], 0).
