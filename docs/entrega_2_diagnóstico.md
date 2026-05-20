# Diagnóstico e Proposta de Melhoria para o In-App Browser

## 1. Diagnóstico do Problema e Oportunidade

No teste A/B/C anterior, a Variante A (controle, sem opção de saída para navegador externo) obteve a melhor performance em Taxa de Conversão e Receita por Visita (RPV), enquanto as variantes B e C, que ofereciam a opção de sair para o navegador externo, não conseguiram superá-la. De fato, a Variante B apresentou uma diminuição estatisticamente significativa nessas métricas. Isso sugere que simplesmente oferecer uma saída para o navegador externo, sem um contexto ou um gatilho inteligente, pode criar mais atrito do que benefício para o usuário, prejudicando sua jornada de compra e, por sua vez, impactando as métricas de negócio.

No entanto, a análise revelou que uma pequena fração de usuários nas variantes B e C, que escolheram sair para o navegador externo, ainda completou transações, o que gerou comissão. Isso nos leva a uma oportunidade: há situações específicas em que o In-App Browser (IAB) pode não ser a melhor escolha para finalizar uma compra, seja devido a problemas de compatibilidade com o site do parceiro, à complexidade dos fluxos de login (como o login social) ou à preferência do usuário por um ambiente de navegação mais familiar, onde suas credenciais já estão salvas. A questão do navegador externo não falhou no pressuposto de que ele pode ser útil, mas sim na maneira como isso foi apresentado, possivelmente de forma muito genérica ou disruptiva.

## 2. Por que vale priorizar isso agora?

Priorizar uma melhoria focada na saída inteligente para navegador externo é crucial neste momento por duas razões principais:

1. Mitigação de Perdas de Conversão: O desempenho inferior das variantes B e C no teste anterior indica que estamos perdendo oportunidades de conversão quando o IAB não consegue oferecer uma experiência ideal. Ao identificar e endereçar esses pontos de fricção de forma inteligente, podemos recuperar essas conversões perdidas e, potencialmente, aumentar a RPV geral.
2. Otimização da Experiência do Usuário: aprimorar a experiência do usuário é fundamental para a retenção e satisfação. Oferecer uma alternativa proativa e contextualizada quando o IAB se mostra inadequado demonstra que o Méliuz entende as necessidades do usuário e busca facilitar sua jornada, mesmo que isso signifique temporariamente sair do ambiente do aplicativo. Isso se alinha com o propósito de desbloquear relações inteligentes de consumo e criar conexões Ganha-Ganha-Ganha.

## 3. Hipótese da Melhoria

Hipótese: Ao implementar uma **sugestão contextualizada e proativa para abrir a página do parceiro em um navegador externo, acionada por gatilhos específicos de alta fricção dentro do In-App Browser (e.g., detecção de formulários de login social, erros de carregamento de página, ou padrões de navegação que indicam dificuldade), aumentaremos a Taxa de Conversão e a Receita por Visita (RPV) para os usuários impactados por esses gatilhos, sem impactar negativamente a experiência da maioria dos usuários que não enfrentam tais problemas.

## 4. Solução proposta: saída inteligente para navegador externo.

A solução proposta é a implementação de um mecanismo de Saída Inteligente para Navegador Externo no In-App Browser. Este mecanismo não será uma opção genérica e sempre presente, mas sim uma sugestão contextualizada, apresentada ao usuário apenas quando gatilhos específicos de alta fricção forem detectados. O objetivo é intervir proativamente para evitar o abandono da jornada de compra, oferecendo uma alternativa mais robusta para a conclusão da transação.

**4.1. Gatilhos Iniciais (MVP)**

Para o MVP, focaremos nos seguintes gatilhos para apresentar a sugestão de saída:

- Detecção de Formulários de Login Social: Quando o IAB identificar a presença de botões ou links para login via redes sociais (e.g., Google, Facebook, Apple ID) que sabidamente causam problemas de compatibilidade ou redirecionamento dentro do IAB.
- Erros de Carregamento de Página: Quando uma página do parceiro falhar ao carregar completamente ou apresentar erros persistentes dentro de um tempo limite pré-definido.
- Detecção de Pop-ups Bloqueados: Quando o IAB identificar que um pop-up essencial para a jornada (e.g., para autenticação, seleção de frete) está sendo bloqueado ou não está funcionando corretamente.

**4.2. Fluxo de Interação**

Quando um dos gatilhos for acionado:

1. Uma modal ou banner discreto será exibido na parte inferior ou superior da tela do IAB, informando o usuário sobre a dificuldade e sugerindo a abertura da página em um navegador externo.
2. A mensagem será clara e objetiva, explicando o benefício de abrir no navegador externo (e.g., "Parece que você está com dificuldades para fazer login. Que tal continuar no seu navegador padrão para uma experiência mais fluida?").
3. Serão apresentadas duas opções: "Abrir no Navegador Externo" e "Continuar no Aplicativo".
4. Ao selecionar "Abrir no Navegador Externo", a URL atual do parceiro será aberta no navegador padrão do dispositivo do usuário, com os parâmetros de rastreamento de cashback preservados.
5. Ao selecionar "Continuar no Aplicativo", a modal/banner será fechada, e o usuário permanecerá no IAB.

## 5. Escopo do MVP

O MVP (Produto Mínimo Viável) desta melhoria incluirá:

- Implementação dos três gatilhos iniciais: detecção de formulários de login social, erros de carregamento de página e detecção de pop-ups bloqueados.
- Desenvolvimento da interface da modal/banner de sugestão de saída, com as opções "Abrir no Navegador Externo" e "Continuar no Aplicativo".
- Garantia de que os parâmetros de rastreamento de cashback sejam preservados ao abrir a URL no navegador externo.
- Instrumentação completa para coletar dados sobre a exibição da sugestão, a interação do usuário com ela (cliques em "Abrir no Navegador Externo" ou "Continuar no Aplicativo") e o impacto nas métricas de conversão e RPV.

## 6. Critérios de Aceite

- A modal/banner de sugestão de saída deve ser exibida corretamente quando um dos gatilhos for acionado.
- A opção "Abrir no Navegador Externo" deve redirecionar o usuário para o navegador padrão do dispositivo, mantendo a URL do parceiro e os parâmetros de rastreamento.
- A opção "Continuar no Aplicativo" deve fechar a sugestão e manter o usuário no IAB.
- A instrumentação deve registrar todos os eventos de exibição e interação com a sugestão de saída, bem como as transações subsequentes.
- A funcionalidade deve ser estável e não introduzir novos bugs ou regressões no IAB.
- A experiência do usuário deve ser fluida e não intrusiva.

## 7. Instrumentação

Para esta melhoria, a instrumentação será fundamental para entender o comportamento do usuário e medir o impacto. Serão utilizados parâmetros de tracking na URL e eventos de analytics.

**7.1. Parâmetros de Tracking (UTMs e mz_*)**

Ao redirecionar para o navegador externo, a URL do parceiro será enriquecida com os seguintes parâmetros:

- utm_source=meliuz_app
- utm_medium=inapp_browser
- utm_campaign=smart_exit
- utm_content={trigger_type} (e.g., login_social, page_error, popup_blocked)
- mz_redirect=browserdefault
- mz_test_gotoexternalbrowser=smart_exit_test (para identificar esta nova variante no teste A/B)

**7.2. Eventos e Propriedades de Analytics**

Serão registrados os seguintes eventos:

- smart_exit_suggestion_displayed:
- property: trigger_type (e.g., login_social, page_error, popup_blocked)
- property: partner_id
- property: visit_id
- smart_exit_accepted:
- property: trigger_type
- property: partner_id
- property: visit_id
- smart_exit_declined:
- property: trigger_type
- property: partner_id
- property: visit_id

## 8. Métricas de Sucesso e Guardrails

**8.1. Métrica Primária**

- Receita por Visita (RPV) para usuários expostos aos gatilhos: Aumentar a RPV para o grupo de usuários que visualiza a sugestão de saída inteligente, comparado a um grupo de controle que não a visualiza (ou visualiza uma versão diferente).

**8.2. Métricas Secundárias**

- Taxa de Conversão para usuários expostos aos gatilhos: Aumentar a taxa de conversão para o grupo de usuários que visualiza a sugestão de saída inteligente.
- Taxa de Aceitação da Sugestão de Saída: Proporção de usuários que clicam em "Abrir no Navegador Externo" após a exibição da sugestão.
- Taxa de Abandono do IAB: Redução da taxa de abandono do IAB para usuários expostos aos gatilhos.

**8.3. Guardrails**

- RPV Geral: A RPV geral do aplicativo não deve cair. A melhoria deve ser incremental e não prejudicar a performance da maioria dos usuários que não são impactados pelos gatilhos.
- Taxa de Conversão Geral: A taxa de conversão geral do aplicativo não deve cair.
- Estabilidade do IAB: A introdução da funcionalidade não deve gerar bugs ou instabilidades no IAB.
- Privacidade do Usuário: Garantir que nenhum dado sensível do usuário seja exposto ou coletado indevidamente durante o redirecionamento.

## 9. Riscos, Dependências e Fora de Escopo

**9.1. Riscos**

- Falsa Positivos nos Gatilhos: O sistema pode identificar erroneamente situações de fricção, exibindo a sugestão de saída desnecessariamente e causando interrupção na jornada.
- Perda de Rastreamento: Apesar dos esforços de instrumentação, pode haver cenários onde o rastreamento de cashback seja perdido ao redirecionar para o navegador externo, impactando a atribuição.
- Experiência Fragmentada: Se a transição entre o IAB e o navegador externo não for suave, pode gerar uma experiência fragmentada e confusa para o usuário.

**9.2. Dependências**

- Time de Engenharia Mobile: Para implementação dos gatilhos, da interface e do redirecionamento.
- Time de Analytics/Dados: Para configurar e validar a instrumentação, e para a análise dos resultados do teste.
- Time de Parcerias/Negócios: Para garantir que os termos de rastreamento com os parceiros sejam compatíveis com o redirecionamento para navegador externo.

**9.3. Fora de Escopo do MVP**

- Implementação de gatilhos adicionais além dos três definidos.
- Personalização da mensagem ou do design da sugestão de saída com base no parceiro ou no tipo de usuário.
- Abertura de navegadores externos específicos (e.g., Chrome Custom Tabs, Safari View Controller) em vez do navegador padrão do dispositivo.

## 10. Outros Pontos Relevantes

- Testes A/B: A melhoria será lançada como um teste A/B para comparar a performance do grupo com a "Saída Inteligente" versus um grupo de controle (que mantém o comportamento atual do IAB, sem a sugestão de saída).
- Iteração: Os resultados do teste A/B serão analisados para refinar os gatilhos, a interface e a mensagem da sugestão de saída, permitindo iterações futuras para otimizar ainda mais a funcionalidade.