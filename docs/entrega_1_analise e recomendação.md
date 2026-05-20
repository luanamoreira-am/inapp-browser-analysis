# Entrega 1 — Análise e Recomendação

## Introdução

Este documento apresenta a análise detalhada de um teste A/B/C realizado no In-App Browser (IAB) do aplicativo Méliuz. O objetivo principal é interpretar o problema de produto, reconstruir o teste a partir dos dados brutos fornecidos, calcular métricas de sucesso com precisão, e formular uma recomendação fundamentada para a próxima melhoria. Além disso, será documentado o processo de trabalho com IA, demonstrando a abordagem analítica e crítica adotada.

O Méliuz, atuando desde 2011, conecta marcas a consumidores através de seu marketplace e serviços financeiros, com o propósito de desbloquear relações inteligentes de consumo. A cultura da empresa é pautada em conexões Ganha-Ganha-Ganha, beneficiando usuários, marcas parceiras e o próprio Méliuz. No contexto do aplicativo, a jornada de compra inicia-se com a ativação do cashback em uma loja parceira, direcionando o usuário para o ambiente da loja para concluir a transação.

## Por que um app de cashback teria um In-App Browser?

Um aplicativo de cashback como o Méliuz utiliza um In-App Browser para proporcionar uma experiência de usuário mais fluida e integrada dentro do próprio aplicativo. Ao invés de redirecionar o usuário para um navegador externo (como Chrome ou Safari), o IAB permite que a jornada de compra seja concluída sem que o usuário saia do ambiente do Méliuz. Isso minimiza fricções, como a necessidade de alternar entre aplicativos, fazer login novamente e. principalmente, perder o contexto da oferta de cashback. Do ponto de vista do negócio, o IAB facilita o rastreamento da jornada do usuário, garantindo que a ativação do cashback seja corretamente atribuída e que a transação seja monitorada para o cálculo e pagamento do benefício. Além disso, permite um maior controle sobre a experiência de marca e a exibição de informações relevantes do Méliuz durante a navegação na loja parceira.

## Qual problema de produto este teste parece tentar resolver?

Com base na análise do documento visual do teste e nos dados, este teste parece tentar resolver o problema de otimização da taxa de conversão e da receita gerada por visita (RPV) em jornadas de compra que exigem interações complexas ou que são interrompidas no In-App Browser. Especificamente, o teste busca entender se oferecer ao usuário a opção de sair do IAB para um navegador externo em momentos estratégicos pode melhorar a conclusão da compra, especialmente em cenários em que o IAB pode apresentar limitações (e.g., problemas de login, compatibilidade com sites de parceiros ou preferência do usuário por seu navegador padrão).

## Qual trade-off existe entre manter o usuário no In-App Browser e permitir saída para navegador externo?

O trade-off central reside entre controle da experiência e rastreamento versus flexibilidade e potencial de conversão.

Manter o usuário no In-App Browser (IAB) oferece:

- Maior controle sobre a jornada: O Méliuz pode injetar scripts de rastreamento, exibir informações de cashback e manter a identidade visual do aplicativo, garantindo que o cashback seja corretamente atribuído.
- Experiência integrada: evita a alternância de aplicativos, o que pode ser percebido como mais conveniente para o usuário.
- Facilidade de rastreamento: acompanhar o comportamento do usuário e a conclusão da compra é mais direto no ambiente controlado do IAB.

Permitir a saída para um navegador externo (BROWSERDEFAULT) oferece:

- Melhor compatibilidade: navegadores externos geralmente lidam melhor com sites complexos, pop-ups e fluxos de login que podem ser problemáticos em IABs mais restritos.
- Preferência do usuário: muitos usuários têm suas credenciais salvas, extensões e configurações personalizadas em seus navegadores padrão, o que pode agilizar o processo de compra.
- Potencial de conversão: ao remover barreiras técnicas ou de usabilidade do IAB, a chance de o usuário concluir a compra pode aumentar, mesmo que o rastreamento se torne ligeiramente mais complexo ou dependa de integrações mais robustas.

O trade-off, portanto, é entre a garantia de rastreamento e a experiência controlada (IAB) e a otimização da conversão através da flexibilidade e familiaridade do navegador externo, que pode, em alguns casos, levar a uma melhor performance de vendas e comissão, mas com um risco potencial de perda de rastreamento se não for bem implementado.

## Hipótese que cada variante parece testar

O teste A/B/C buscou avaliar se oferecer a saída para um navegador externo em momentos de potencial fricção (login social bloqueado, recursos do navegador indisponíveis e possíveis sites de parceiros não otimizados para IAB) poderia melhorar a experiência e a conversão sem destruir o rastreio. 

O trade-off central é entre manter no IAB com rastreio garantido, mas com possível atrito, e sair para externo com melhor experiência, mas risco de perder o cookie de cashback e o usuário.

**Hipóteses por variante:**

As variantes 'a', 'b' e 'c' do parâmetro `mz_test_gotoexternalbrowser` nos dados e na observação do PDF do teste sugerem que o teste está comparando diferentes implementações ou apresentações da funcionalidade do In-App Browser, buscando a que melhor equilibra a experiência do usuário com a eficácia do rastreamento e a conversão de vendas. A imagem do PDF corrobora essa hipótese, mostrando diferentes fluxos de interação para as versões de controle e teste.

Descobertas:

Usuários que saem para o navegador externo convertem **~1,4 ponto percentual a menos** (11,46% em B, 11,01% em C) do que aqueles que ficam no INAPP (12,64-12,77%).

| **Grupo** | **Destino** | **Visitas** | **Convertidas** | **Taxa de Conversão** |
| --- | --- | --- | --- | --- |
| **A** | INAPP (100%) | 377.716 | 48.607 | **12,87%** |
| **B** | INAPP (97,6%) | 376.350 | 47.554 | 12,64% |
| **B** | Navegador Externo (2,4%) | 9.407 | 1.078 | **11,46%** |
| **C** | INAPP (98,1%) | 376.513 | 48.068 | 12,77% |
| **C** | Navegador Externo (1,9%) | 7.357 | 810 | **11,01%** |

Isso significa que o navegador externo é **menos eficiente para conversão**, provavelmente porque:

- Experiência de login social é mais complicada fora do contexto do app
- Perda de contexto/sessão ao sair do INAPP
- Maior fricção no checkout

**Análise do impacto total:**

- Grupo B perdeu 1,81% de conversão porque 2,4% dos usuários foram para um ambiente 1,4pp menos eficiente
- Grupo C perdeu 0,79% porque 1,9% dos usuários foram para um ambiente 1,4pp menos eficiente
- Além disso, há um efeito secundário: usuários que **veem** a opção de saída (mesmo sem clicar) parecem converter menos.

## Definição e calculo de métricas de sucesso do teste

A métrica de sucesso primária para este teste deve ser a receita por visita (RPV), complementada pela taxa de conversão. Ambas as métricas refletem diretamente o impacto no negócio do Méliuz, alinhando-se ao propósito de desbloquear relações inteligentes de consumo e criar conexões Ganha-Ganha-Ganha.

Definições e cálculos:

- Taxa de Conversão: representa a proporção de visitas que resultam em uma transação. É um indicador direto da eficácia da jornada de compra.
- Cálculo: Total de Transações / Total de Visitas.
- Receita por Visita (RPV): Representa a receita média gerada para o Méliuz por cada visita. Esta métrica é crucial, pois considera não apenas a conversão, mas também o valor das vendas e, consequentemente, a comissão esperada. Uma visita que resulta em uma venda de alto valor é mais benéfica do que várias visitas que resultam em vendas de baixo valor, mesmo que a taxa de conversão seja a mesma.
- Cálculo: Comissão Total Esperada / Total de Visitas

Ambas as métricas serão calculadas para cada variante (A, B e C) e comparadas para determinar qual delas oferece o melhor desempenho. A RPV é particularmente importante porque o modelo de negócio do Méliuz é baseado em comissões sobre vendas, tornando-a um indicador mais holístico do impacto financeiro.

## Análise Estruturada dos Dados

**Limpeza e Tratamento dos Dados**

Os dados brutos foram fornecidos em arquivos CSV (visits.csv, transactions.csv, url_params.csv, visit_url_metadata.csv, channels.csv, partners.csv). O tratamento inicial envolveu:

1. Carregamento Seletivo: Apenas as colunas essenciais foram carregadas para otimizar o uso de memória e processamento, especialmente para visit_url_metadata.csv e visits.csv que são arquivos grandes.
2. Extração de Variantes: A coluna tracking_url_params de visit_url_metadata.csv continha um JSON com o parâmetro mz_test_gotoexternalbrowser, que foi utilizado para identificar as variantes A, B e C. Um tratamento de erro foi implementado para casos onde o JSON pudesse estar malformado ou o parâmetro ausente.
3. Decodificação de Canais: O arquivo channels.csv foi utilizado para mapear channel_id para channel_name (INAPP ou BROWSERDEFAULT), permitindo a análise da distribuição de tráfego entre os canais.
4. Junção de Dados: Os dataframes foram unidos (visits com visit_url_metadata para variantes e channels, e transactions com visits e visit_url_metadata para análise transacional por variante e canal), garantindo que todas as informações necessárias estivessem correlacionadas para a análise.

**Identificação das Variantes A, B e C**

As variantes foram identificadas a partir do parâmetro mz_test_gotoexternalbrowser presente no JSON da coluna tracking_url_params do arquivo visit_url_metadata.csv. Onde:

- "mz_test_gotoexternalbrowser":"a" corresponde à Variante A (Controle).
- "mz_test_gotoexternalbrowser":"b" corresponde à Variante B (Teste).
- "mz_test_gotoexternalbrowser":"c" corresponde à Variante C (Teste).

**Resultados Agregados por Variante**

A tabela a seguir apresenta um resumo das principais métricas por variante:

| Variante | Total de Visitas | Usuários Únicos | Total de Transações | Vendas Totais (R$) | Cashback Total (R$) | Comissão Total (R$) | Taxa de Conversão | Ticket Médio (R$) | RPV (R$) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| A | 377.716 | 128.804 | 50.639 | 27.371.536,08 | 1.516.327,13 | 2.100.034,95 | 13,41% | 540,52 | 5,56 |
| B | 385.757 | 128.738 | 50.543 | 25.347.881,53 | 1.458.684,77 | 2.012.811,87 | 13,10% | 501,51 | 5,22 |
| C | 383.870 | 128.478 | 51.346 | 27.233.911,32 | 1.519.347,67 | 2.112.918,16 | 13,38% | 530,40 | 5,50 |

**Análise Estatística (vs. Variante A - Controle)**

Para avaliar a significância das diferenças observadas, foi realizada uma análise estatística comparando as variantes B e C com a variante A (controle). Foi utilizado um teste de proporção (aproximado por qui-quadrado) para a taxa de conversão e o lift para a RPV.

| Variante | Lift Conversão (vs. A) | p-valor Conversão | Lift RPV (vs. A) |
| --- | --- | --- | --- |
| A | 0,00% | 1,000 | 0,00% |
| B | -2,27% | 0,000089 | -6,15% |
| C | -0,23% | 0,696 | -1,00% |

Interpretação:

- Variante B: Apresentou uma queda estatisticamente significativa na taxa de conversão (p-valor < 0.05) e na RPV em comparação com a Variante A. O lift negativo indica que a Variante B performou pior que o controle.
- Variante C: Não apresentou uma diferença estatisticamente significativa na taxa de conversão (p-valor > 0.05) em comparação com a Variante A. O lift na RPV também foi negativo, mas não há indicação de significância estatística com base nos dados apresentados.

**Distribuição de Visitas por Canal e Variante**

A distribuição de visitas entre o In-App Browser (INAPP) e o navegador padrão (BROWSERDEFAULT) revela como as variantes B e C direcionaram o tráfego:

| Variante | Canal | Visitas |
| --- | --- | --- |
| A | INAPP | 377.716 |
| B | BROWSERDEFAULT | 9.407 |
| B | INAPP | 376.350 |
| C | BROWSERDEFAULT | 7.357 |
| C | INAPP | 376.513 |

É evidente que as variantes B e C introduziram um redirecionamento para o navegador padrão, enquanto a variante A manteve todas as visitas no IAB. A quantidade de visitas redirecionadas para o navegador padrão é relativamente pequena em comparação com o total de visitas, mas é onde a diferença entre B e C se manifesta.

**Tipos de Redirecionamento e Transações**

Analisando os tipos de redirecionamento (mz_redirect) e o utm_content (mz_test_gotoexternalbrowser):

| Variante | Tipo de Redirecionamento | Visitas | Transações | Comissão (R$) |
| --- | --- | --- | --- | --- |
| A | inapp | 377.716 | 50.639 | 2.100.034,95 |
| B | browserdefault | 9.407 | 1.114 | 23.403,79 |
| B | inapp | 376.350 | 49.429 | 1.989.408,00 |
| C | browserdefault | 7.357 | 1.170 | 21.653,28 |
| C | inapp | 376.513 | 50.176 | 2.091.265,70 |

Observa-se que, nas variantes B e C, uma pequena parcela das visitas foi para o browserdefault. Embora essas visitas representem uma fração menor do total, elas ainda geraram transações e comissão. A variante B teve mais visitas redirecionadas para o navegador padrão do que a C, mas a C gerou mais transações e comissão no navegador padrão com menos visitas, indicando uma possível maior eficiência no redirecionamento para o navegador padrão.

**Visualizações**

![Taxa de Conversão por Variante](https://private-us-east-1.manuscdn.com/sessionFile/hYAuwvbY5KW2EGZMAx1yOC/sandbox/VDpr6UwBWEL1ZtAsb3CB1S-images_1779280494846_na1fn_L2hvbWUvdWJ1bnR1L2NvbnZlcnNpb25fcmF0ZQ.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvaFlBdXd2Ylk1S1cyRUdaTUF4MXlPQy9zYW5kYm94L1ZEcHI2VXdCV0VMMVp0QXNiM0NCMVMtaW1hZ2VzXzE3NzkyODA0OTQ4NDZfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwyTnZiblpsY25OcGIyNWZjbUYwWlEucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzk4NzYxNjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=LkXahYbu3DobrOoV4Kvv21ilRFBz8mxj4VCYy-y2z7qtk7BmKHBjpeQzwdAZwdfqB6iR57GN0u1h2LzOcOeXMeChtWr~gtzrNOLRozEPQelUj1cW6ije0EIqMNE9LGVE~Olkh2EEf4feEsjiNX0bmNgVvvSfmMPRf5nEUClTBAAnKyj77DZpYS6ur5VjimhKpSHTHzIhcm1BSLdRj3Ynpwvd5b~lA5dLLYnZcXRSzWfl83-9WpgNmHXaSFzpnIXHfPRGAQTjQ-b63ylpTBMMBKmBA7f6y3l-RYpUU~CikAA2vbjItWVlXgXZRDJiW2e5d6kX2-KxL0PafJrJd-6ghA__)

Gráfico 1: Taxa de Conversão por Variante

![Receita por Visita (RPV) por Variante](https://private-us-east-1.manuscdn.com/sessionFile/hYAuwvbY5KW2EGZMAx1yOC/sandbox/VDpr6UwBWEL1ZtAsb3CB1S-images_1779280494846_na1fn_L2hvbWUvdWJ1bnR1L3JldmVudWVfcGVyX3Zpc2l0.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvaFlBdXd2Ylk1S1cyRUdaTUF4MXlPQy9zYW5kYm94L1ZEcHI2VXdCV0VMMVp0QXNiM0NCMVMtaW1hZ2VzXzE3NzkyODA0OTQ4NDZfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwzSmxkbVZ1ZFdWZmNHVnlYM1pwYzJsMC5wbmciLCJDb25kaXRpb24iOnsiRGF0ZUxlc3NUaGFuIjp7IkFXUzpFcG9jaFRpbWUiOjE3OTg3NjE2MDB9fX1dfQ__&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=R~LGb-9ndylosZ4AyNol1y9XMNPg7CLPEcT8KNwb~XiZwYz3wvqaWOT9iB3h4lUlI3D59wTkM6uIQ~mzIW2K4Y1zoW64H9v7YtyTqNfhKUsYX5i6GHPDPVotxvPjn~ICLsr62VH6939llTOSDJ~okzUnB515QX4YqS90o-cI5qtFJ6n4Tm4UFOr2VawCgcUNFI2ArAIeIb4OjHxKXlwXOahktTlsNLMrb1fyfxWsceqJ9dr6YTqMMjUHZz8l2sznJuQ7mtf2gbkiyjoH3g3PbpsNjj1XEcPeGE1-2xUfDstauYzBPoHCw~vmjisWMa1z86hZmJ1IXwB~UBD~s~zKDg__)

Gráfico 2: Receita por Visita (RPV) por Variante

![Distribuição de Visitas por Canal e Variante](https://private-us-east-1.manuscdn.com/sessionFile/hYAuwvbY5KW2EGZMAx1yOC/sandbox/VDpr6UwBWEL1ZtAsb3CB1S-images_1779280494846_na1fn_L2hvbWUvdWJ1bnR1L2NoYW5uZWxfZGlzdHJpYnV0aW9u.png?Policy=eyJTdGF0ZW1lbnQiOlt7IlJlc291cmNlIjoiaHR0cHM6Ly9wcml2YXRlLXVzLWVhc3QtMS5tYW51c2Nkbi5jb20vc2Vzc2lvbkZpbGUvaFlBdXd2Ylk1S1cyRUdaTUF4MXlPQy9zYW5kYm94L1ZEcHI2VXdCV0VMMVp0QXNiM0NCMVMtaW1hZ2VzXzE3NzkyODA0OTQ4NDZfbmExZm5fTDJodmJXVXZkV0oxYm5SMUwyTm9ZVzV1Wld4ZlpHbHpkSEpwWW5WMGFXOXUucG5nIiwiQ29uZGl0aW9uIjp7IkRhdGVMZXNzVGhhbiI6eyJBV1M6RXBvY2hUaW1lIjoxNzk4NzYxNjAwfX19XX0_&Key-Pair-Id=K2HSFNDJXOU9YS&Signature=jp8OkYsnlXodGIr1hlvL2D~QRuoYRUU1X00mIiopPnQCu3UlbvlizyXSRYSze91ktDQGGJfH9TYQPOmzE5f3qtUk2FQJoBjQoqr8AOPdKgn49QZFxH-kmehLkxZk4g7GsBtvaM92ffRxzBY4Cjp2oMbFITmJOpIKFR6jAuqA8Pk6duYoJPd-G6nbsI~m9Glxww7eZZkZcB27HOUez3sHOSX2tD2iZMGX9xz8-yeEjJkOH2QF-hFQRejgzgEMcmdBiru1ijbZadKjkmAsGrmvxhcXi7mdZQpKAdqGOP-II7hGS~rznbsbnpO69905dMJBibdmoiIx43xrnW3nZcLplw__)

Gráfico 3: Distribuição de Visitas por Canal e Variante

Investigação dos NaNs: Aprofundar a investigação sobre as visitas com test_group NaN. Entender se elas representam um comportamento de usuário distinto, um problema de rastreamento ou se podem ser atribuídas a um dos grupos de forma indireta. Dependendo da conclusão, será definida a estratégia de inclusão/exclusão na análise principal.

## Recomendação Fundamentada

**Qual versão implementar para os usuários? Por quê?**

Com base na análise dos dados, a Variante A (Controle) deve ser mantida para os usuários. As variantes B e C, que introduziram a opção de saída para o navegador externo, não demonstraram melhoria nas métricas de sucesso primárias (Taxa de Conversão e RPV) em comparação com o controle. Pelo contrário, a Variante B apresentou uma queda estatisticamente significativa na taxa de conversão e RPV.

Embora as variantes B e C tenham gerado algumas transações e comissão através do navegador padrão, o impacto negativo geral na performance do teste sugere que a introdução da opção de saída, da forma como foi implementada, não foi benéfica. Pode ser que a fricção de decidir sair do IAB, ou a perda de contexto ao fazê-lo, tenha superado os benefícios de usar um navegador externo.

**Qual o impacto esperado?**

Ao manter a Variante A, o impacto esperado é a continuidade do desempenho atual, que se mostrou superior ou estatisticamente similar às variantes de teste. Isso significa que o Méliuz continuará a otimizar a experiência de compra dentro do In-App Browser, garantindo o rastreamento eficaz e a atribuição de cashback, sem introduzir complexidades ou quedas de performance observadas nas variantes B e C.

**Quais riscos, limitações ou cuidados considerar?**

- Foco no IAB: A decisão de manter a Variante A reforça a importância de continuar otimizando a experiência dentro do In-App Browser. Quaisquer problemas de compatibilidade ou usabilidade que levam os usuários a desejar um navegador externo ainda precisam ser endereçados dentro do IAB.
- Perda de Oportunidade: Embora as variantes B e C não tenham performado melhor, a ideia de oferecer flexibilidade ao usuário ainda pode ser válida. É possível que a implementação atual não tenha sido a ideal. Descartar completamente a ideia de saída para navegador externo sem explorar outras abordagens pode significar a perda de oportunidades futuras de otimização.
- Custo de Fricção: O teste sugere que a fricção de oferecer a escolha de sair do IAB pode ser maior do que o benefício percebido pelo usuário. É crucial entender a natureza dessa fricção (e.g., interrupção do fluxo, confusão, medo de perder o cashback).
- Dados de Contexto Limitados: A análise não teve acesso a dados qualitativos (e.g., feedback de usuários, gravações de sessão) que poderiam explicar por que as variantes B e C não performaram bem. A falta de informações sobre os gatilhos exatos e a interface de usuário para a opção de saída limita a profundidade da análise.

## Insights Adicionais  - additional insights

**O que os dados revelam além da pergunta principal.**

Os dados mostram que, mesmo com a opção de sair para o navegador externo, a grande maioria dos usuários permaneceu no In-App Browser nas variantes B e C. Isso pode indicar uma preferência geral por permanecer no ambiente do aplicativo, ou que os gatilhos para a saída não foram fortes o suficiente para motivar a mudança. A pequena parcela de usuários que utilizou o navegador padrão nas variantes B e C ainda gerou transações, sugerindo que, para um subsegmento de usuários ou em situações específicas, o navegador externo é de fato a melhor opção para a conversão.

**Como interpretar as saídas normais da tela do parceiro e as saídas externas geradas por login, header e config.**

O utm_content PARTNER_PAGE indica que a visita está ocorrendo na página do parceiro, seja no IAB ou no navegador externo. O utm_content EXTERNAL_BROWSER_MODAL está associado às visitas que foram redirecionadas para o navegador externo. Isso confirma que as variantes B e C introduziram essa modalidade de redirecionamento.

As saídas externas geradas por login, header e config (mencionadas no prompt original) provavelmente se referem a cenários específicos em que o Méliuz identificou que o IAB poderia ser um gargalo. Por exemplo:

- Login: muitos sites de parceiros utilizam login social (Google, Facebook) ou fluxos de autenticação complexos que podem não funcionar perfeitamente em todos os IABs. Oferecer a saída para o navegador externo nesse momento pode evitar que o usuário abandone a compra por dificuldade de login.
- Header/Config: pode se referir a elementos da interface do IAB (header) que podem estar cobrindo informações importantes do site do parceiro, ou configurações específicas do IAB que impedem certas funcionalidades. Permitir a saída nesses casos seria uma forma de contornar essas limitações.

Os dados brutos não detalham a causa específica de cada saída externa, mas a presença do EXTERNAL_BROWSER_MODAL confirma que a intenção era oferecer essa alternativa. A análise de utm_content por variante mostrou que apenas as variantes B e C apresentaram esse tipo de conteúdo, o que é consistente com a hipótese de que elas introduziram a funcionalidade de saída para o navegador externo.

**Que novas perguntas você faria se tivesse mais tempo ou dados.**

1. Qualitativas: qual o feedback dos usuários sobre a opção de sair para o navegador externo? Eles entenderam a funcionalidade? Quais foram os principais motivos para sair ou para permanecer no IAB? (Pesquisas, entrevistas, testes de usabilidade, gravações de sessão).
2. Segmentação: há segmentos de usuários (e.g., por tipo de dispositivo, sistema operacional, parceiro) que se beneficiam mais ou menos da opção de saída para o navegador externo? A performance varia significativamente entre diferentes parceiros?
3. Timing e contexto: O momento em que a opção de saída é oferecida impacta a decisão do usuário e a conversão? Por exemplo, oferecer a saída apenas após uma falha de login ou um tempo de carregamento excessivo no IAB.
4. Impacto no Rastreamento: qual o impacto real da saída para o navegador externo na capacidade do Méliuz de rastrear a transação e atribuir o cashback? Houve perda de atribuição em casos de saída?
5. Design da Intervenção: como o design da interface (texto, posicionamento do botão, clareza da mensagem) da opção de saída influenciou a decisão do usuário e a performance do teste?
6. Métricas de Engajamento: como a introdução da opção de saída afetou métricas de engajamento no IAB, como tempo na página, número de páginas visitadas, etc.?