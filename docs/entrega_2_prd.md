# **Quebra Técnica da PRD: Saída Inteligente para Navegador Externo**

Este documento detalha os entregáveis técnicos e o plano de implementação para a funcionalidade de "Saída Inteligente para Navegador Externo" no In-App Browser, conforme especificado na PRD. O objetivo é fornecer ao time de engenharia um guia claro para o desenvolvimento, instrumentação e validação da melhoria.

## 1. Entregáveis Técnicos

Os principais entregáveis técnicos para esta melhoria podem ser divididos nas seguintes categorias:

**1.1. Desenvolvimento do Mecanismo de Detecção de Gatilhos**

- Módulo de Detecção de Login Social: Implementar lógica no IAB para identificar padrões de URLs ou elementos HTML que correspondam a fluxos de login social (e.g., accounts.google.com, facebook.com/login, botões com classes ou IDs específicos para login social). Este módulo deve ser configurável para adicionar novos padrões facilmente.
- Módulo de Monitoramento de Erros de Carregamento: Desenvolver um mecanismo para detectar falhas no carregamento de recursos da página (imagens, scripts, CSS) ou timeouts de carregamento da página completa. Isso pode envolver a interceptação de eventos de erro do WebView ou a medição do tempo de carregamento.
- Módulo de Detecção de Pop-ups Bloqueados: Implementar lógica para identificar tentativas de abertura de novas janelas (pop-ups) que são bloqueadas pelo WebView ou que não são tratadas corretamente, impedindo a progressão do usuário.

**1.2. Desenvolvimento da Interface de Intervenção (Banner/Modal)**

- Componente UI do Banner/Modal: Criar um componente de interface de usuário (UI) para o banner ou modal de sugestão de saída. Este componente deve ser não intrusivo, responsivo e seguir as diretrizes de design do Méliuz. Deve conter o texto especificado na PRD e os dois botões de ação.
- Lógica de Exibição: Implementar a lógica para exibir o banner/modal apenas quando um dos gatilhos for acionado e o usuário ainda estiver na página do parceiro. Deve haver um controle para evitar exibições repetitivas excessivas na mesma sessão.

**1.3. Desenvolvimento do Mecanismo de Redirecionamento**

- Função de Redirecionamento para Navegador Externo: Desenvolver uma função que, ao ser acionada, abra a URL atual do IAB no navegador padrão do dispositivo do usuário. Esta função deve garantir que todos os parâmetros de rastreamento de cashback (existentes e os novos utm_* e mz_*) sejam corretamente anexados à URL antes do redirecionamento.
- Preservação de Contexto: Assegurar que o estado da sessão do IAB seja preservado ou que o usuário possa retornar ao aplicativo sem perder o contexto, caso decida não interagir com o navegador externo.

**1.4. Instrumentação de Analytics**

- Integração de Eventos: Integrar os eventos de analytics (smart_exit_suggestion_displayed, smart_exit_accepted, smart_exit_declined) com o SDK de analytics do aplicativo. Cada evento deve incluir as propriedades especificadas (trigger_type, partner_id, visit_id).
- Enriquecimento de Parâmetros de URL: Modificar a função de abertura de URLs externas para incluir os novos parâmetros utm_campaign=smart_exit, utm_content={trigger_type}, mz_redirect=browserdefault e mz_test_gotoexternalbrowser=smart_exit_test.

## 2. Quebra do Trabalho em Pequenos Entregáveis (Exemplo)

Para facilitar o desenvolvimento e permitir entregas incrementais, o trabalho pode ser dividido em sprints ou fases, com foco em funcionalidades específicas:

**Sprint 1: Infraestrutura de Detecção e UI Básica**

- Task 1.1: Criar módulo base para detecção de gatilhos no IAB (e.g., um interceptor de requisições ou um observador de DOM).
- Task 1.2: Implementar o gatilho de detecção de login social (identificação de URLs ou elementos HTML).
- Task 1.3: Desenvolver o componente UI do banner/modal de sugestão de saída (apenas layout e botões, sem lógica de exibição).
- Task 1.4: Implementar a função de redirecionamento para navegador externo com preservação de parâmetros básicos.
- Task 1.5: Configurar o evento smart_exit_suggestion_displayed no SDK de analytics.

**Sprint 2: Gatilhos Adicionais e Lógica de Exibição**

- Task 2.1: Implementar o gatilho de monitoramento de erros de carregamento de página.
- Task 2.2: Implementar o gatilho de detecção de pop-ups bloqueados.
- Task 2.3: Desenvolver a lógica para exibir o banner/modal quando um gatilho é acionado (controle de frequência, etc.).
- Task 2.4: Configurar os eventos smart_exit_accepted e smart_exit_declined no SDK de analytics.

**Sprint 3: Refinamento e Testes**

- Task 3.1: Refinar a lógica de preservação de parâmetros de rastreamento para garantir 100% de atribuição.
- Task 3.2: Realizar testes de integração e ponta a ponta para todos os gatilhos e o fluxo de redirecionamento.
- Task 3.3: Testes de performance e estabilidade do IAB com a nova funcionalidade.
- Task 3.4: Validação da instrumentação de analytics em ambiente de homologação.

## 3. Parâmetros de Tracking, Eventos e Propriedades Necessários

Conforme detalhado na PRD , a instrumentação é crítica para a análise. A tabela a seguir resume os pontos de rastreamento:

**3.1. Parâmetros de URL para Redirecionamento**

| Parâmetro | Valor | Descrição |
| --- | --- | --- |
| utm_source | meliuz_app | Origem do tráfego. |
| utm_medium | inapp_browser | Meio de origem. |
| utm_campaign | smart_exit | Campanha/funcionalidade. |
| utm_content | {trigger_type} | Tipo de gatilho (e.g., login_social, page_error, popup_blocked). |
| mz_redirect | browserdefault | Confirma redirecionamento para navegador padrão. |
| mz_test_gotoexternalbrowser | smart_exit_test | Identificador da variante do teste A/B. |

**3.2. Eventos de Analytics**

| Nome do Evento | Propriedades | Descrição |
| --- | --- | --- |
| smart_exit_suggestion_displayed | trigger_type, partner_id, visit_id | Registra a exibição do banner de sugestão. |
| smart_exit_accepted | trigger_type, partner_id, visit_id | Registra o clique em "Abrir no Navegador Externo". |
| smart_exit_declined | trigger_type, partner_id, visit_id | Registra o clique em "Continuar no Aplicativo" ou fechamento do banner. |

## 4. Validação da Instrumentação Antes do Lançamento

A validação da instrumentação é um passo crítico para garantir a confiabilidade dos dados coletados. O processo envolverá:

1. Testes Manuais em Homologação: A equipe de QA e o Product Manager simularão cenários que ativam cada um dos gatilhos (e.g., navegar para uma página de login social, desconectar a internet para simular erro de carregamento, tentar abrir um pop-up). Para cada cenário, será verificado visualmente se o banner de sugestão é exibido corretamente.
2. Monitoramento de Analytics em Tempo Real: Durante os testes manuais, será utilizado um painel de analytics em tempo real (e.g., DebugView do Firebase, ferramentas de debug do Amplitude) para confirmar que os eventos (smart_exit_suggestion_displayed, smart_exit_accepted, smart_exit_declined) estão sendo disparados com as propriedades corretas e os valores esperados para trigger_type, partner_id e visit_id.
3. Verificação de Parâmetros de URL: Ao aceitar a sugestão de saída, a URL aberta no navegador externo será inspecionada para garantir que todos os parâmetros UTM e mz_* estejam presentes e com os valores corretos. Isso pode ser feito usando ferramentas de desenvolvedor do navegador ou extensões específicas.
4. Teste de Atribuição de Cashback: Será realizada uma transação de teste completa após o redirecionamento para o navegador externo, verificando se o cashback é corretamente atribuído à visita original no Méliuz, confirmando a integridade do rastreamento.