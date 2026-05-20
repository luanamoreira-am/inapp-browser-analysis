# Entrega 2 — Quebra Técnica da PRD: Saída Inteligente para Navegador Externo

# PRD: SSO Pré-autenticado dentro do INAPP Browser

Versão: 1.0Data: Maio 2026Proprietário do Produto: Product Manager — ShoppingStatus: Proposta para Aprovação

## 1. Contexto de Negócio

**Visão Geral**

O Méliuz é uma plataforma de cashback que conecta usuários a lojas parceiras. A jornada de compra começa quando o usuário ativa o cashback no app e é redirecionado para a loja via In-App Browser (webview). A conversão é a métrica crítica de negócio: cada 0,5pp de melhoria em taxa de conversão gera R$ 2,5M/mês em receita adicional.

**Contexto do Teste A/B/C (Entrega 1)**

Em dezembro de 2025, testamos oferecer navegador externo como alternativa ao In-App Browser para resolver problemas de autenticação social. Resultado:

- Grupo A (Controle): 12,869% de conversão
- Grupo B (Header): 12,636% (−1,81pp, −R$ 6,76M/mês)
- Grupo C (Config): 12,767% (−0,79pp, −R$ 1,42M/mês)

Conclusão: Oferecer saída prejudica conversão por atrito psicológico (medo de perder cashback).

**Aprendizado Crítico**

O teste provou que o problema não é técnico, é comportamental. Mas isso não significa que o problema de login social não existe. Significa que a solução testada estava errada.

## 2. Problema e Oportunidade

**Problema identificado.**

Login social bloqueado em webview prejudica conversão para um segmento de usuários.

Evidências:

- Usuários que saem para navegador externo convertem 1,4pp menos (11,46% vs 12,64%)
- Quem sai para navegador externo: 2,4% dos usuários (Grupo B)
- Taxa de erro de autenticação em webview: desconhecida (dado faltante crítico)

Mecanismo do Problema:

1. Usuário abre loja no In-App Browser
2. Loja pede login social (Google, Facebook, Apple)
3. OAuth em webview é frágil (bloqueio de cookies de terceiros, redirecionamento quebrado)
4. Usuário recebe erro ou fica preso
5. Usuário abandona ou sai para navegador externo
6. Conversão cai

**Oportunidade**

Resolver o problema de autenticação social SEM criar atrito psicológico.

Se conseguirmos fazer login automático (SSO pré-autenticado), podemos:

- Manter usuário no INAPP (conversão 1,4pp mais alta)
- Sem oferecer confusão de escolha (sem atrito psicológico)
- Beneficiar 100% dos usuários (não apenas 2,4%)

Impacto Financeiro Esperado:

- Melhoria de +0,5pp em conversão = R$ 2,5M/mês
- Redução de abandono no checkout = R$ 1M/mês adicional
- Total: R$ 3,5M/mês em receita incremental

**Por que priorizar agora?**

1. Validação de Problema: Teste A/B/C provou que há problema real (quem sai converte menos)
2. Solução Clara: SSO pré-autenticado é a solução óbvia e viável
3. ROI Alto: R$ 3,5M/mês é impacto significativo
4. Timing: Parceiros estão prontos para integração (OAuth padrão)
5. Aprendizado Anterior: Teste A/B/C nos deu insights para não cometer o mesmo erro

## 3. Hipótese

Se usuários já estão autenticados no Méliuz, não precisam fazer login novamente na loja parceira (SSO pré-autenticado). Isso reduz fricção de autenticação social e aumenta conversão sem criar atrito psicológico.

**Hipótese Detalhada**

| Elemento | Descrição |
| --- | --- |
| Problema | Login social bloqueado em webview prejudica conversão |
| Causa Raiz | OAuth em webview é frágil (bloqueio de cookies, redirecionamento quebrado) |
| Solução | Usar token de autenticação do Méliuz para fazer login automático na loja |
| Mecanismo | Méliuz injeta token no webview → Loja reconhece token → Login automático |
| Resultado esperado | Conversão aumenta +0,5pp (de 12,87% para 13,4%) |
| Usuários beneficiados | 100% (não apenas 2,4% como navegador externo) |
| Atrito psicológico | Zero (não oferece confusão de escolha) |

**Por que essa hipótese é diferente do teste A/B/C?**

| Aspecto | Teste A/B/C | SSO pré-autenticado |
| --- | --- | --- |
| Solução Testada | Oferecer navegador externo | Melhorar autenticação no INAPP |
| Mecanismo | Oferecia escolha | Melhora experiência sem escolha |
| Atrito Psicológico | Alto (medo de perder cashback) | Zero (sem confusão) |
| Aplicação | 2,4% dos usuários | 100% dos usuários |
| Resultado | Falhou (−1,81pp) | Esperado sucesso (+0,5pp) |

## 4. Solução proposta:

**Visão Geral**

Implementar SSO pré-autenticado dentro do In-App Browser para fazer login automático do usuário nas lojas parceiras, eliminando a necessidade de fazer login social novamente.

**Fluxo de Usuário**

```html
1. Usuário abre Méliuz (já logado)
   ↓
2. Usuário vê oferta de cashback em uma loja (ex: Amazon)
   ↓
3. Usuário clica em "Ativar Cashback"
   ↓
4. Méliuz abre In-App Browser com Amazon
   ↓
5. Méliuz injeta token de autenticação no webview
   ↓
6. Amazon reconhece token e faz login automático
   ↓
7. Usuário vê carrinho (sem tela de login)
   ↓
8. Usuário preenche dados de pagamento
   ↓
9. Usuário clica "Comprar"
   ↓
10. Conversão registrada
```

**Componentes Técnicos**

1. Backend Méliuz
- Gerar JWT com informações do usuário
- Assinar e criptografar token
- Injetar token no webview
1. Frontend (In-App Browser)
- Receber token do backend
- Injetar no header HTTP (Authorization)
- Injetar no localStorage (fallback)
1. Integração com Parceiros
- Parceiros validam token
- Parceiros criam sessão automática
- Parceiros pulam tela de login

**Arquitetura**

```html
Plain Text
┌──────────────────────────────────────────────────────────────┐
│ Méliuz App                                                    │
│ ┌────────────────────────────────────────────────────────┐   │
│ │ 1. Usuário faz login                                  │   │
│ │ 2. Backend gera JWT                                   │   │
│ │ 3. JWT armazenado no app (seguro)                     │   │
│ └────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│ In-App Browser (Webview)                                      │
│ ┌────────────────────────────────────────────────────────┐   │
│ │ 1. Abre URL da loja                                   │   │
│ │ 2. Injeta JWT no header HTTP                          │   │
│ │ 3. Injeta JWT no localStorage                         │   │
│ │ 4. Envia requisição para loja                         │   │
│ └────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────┐
│ Loja Parceira (Amazon, Magalu, etc)                          │
│ ┌────────────────────────────────────────────────────────┐   │
│ │ 1. Recebe requisição com JWT                          │   │
│ │ 2. Valida JWT (verifica assinatura)                   │   │
│ │ 3. Extrai informações do usuário                      │   │
│ │ 4. Cria sessão automática                             │   │
│ │ 5. Usuário já está "logado"                           │   │
│ │ 6. Pula tela de login                                 │   │
│ └────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────┘
```

## 5. Escopo do MVP

**Incluso**

1. Geração de Token
- JWT com ID, email, nome do usuário
- Assinado com chave privada Méliuz
- Expiração: 10 minutos
1. Injeção no Webview
- Header HTTP: Authorization: Bearer <JWT>
- localStorage: meliuz_token: <JWT>
- Validação de HTTPS
1. Integração com Top 10 Parceiros
- Amazon, Magalu, Shopee, Americanas, etc.
- Parceiros que já têm OAuth implementado
- Suporte técnico durante integração
1. Teste A/B/C
- Grupo A: INAPP padrão (controle)
- Grupo B: SSO pré-autenticado
- Grupo C: Pré-preenchimento de dados
- Duração: 30 dias
1. Instrumentação
- Eventos de sucesso/erro de autenticação
- Tempo de checkout
- Taxa de conversão por grupo

**Fora de Escopo (V2+)**

1. Suporte a login com Apple ID (apenas Google/Facebook em V1)
2. Integração com todos os 435 parceiros (apenas top 10 em V1)
3. Sincronização de dados de perfil (apenas autenticação em V1)
4. Logout automático (apenas login em V1)
5. Biometria no webview (apenas token em V1)

## 6. Critérios de Aceite

**Funcionalidade**

- [ ]  JWT é gerado corretamente com informações do usuário
- [ ]  JWT é injetado no header HTTP do webview
- [ ]  JWT é injetado no localStorage do webview
- [ ]  JWT expira após 10 minutos
- [ ]  JWT é validado corretamente pelos parceiros
- [ ]  Usuário faz login automático sem ver tela de login
- [ ]  Usuário consegue completar checkout
- [ ]  Conversão é registrada corretamente

**Segurança**

- [ ]  JWT é criptografado (não é plain text)
- [ ]  JWT é assinado com chave privada Méliuz
- [ ]  HTTPS é obrigatório (não funciona em HTTP)
- [ ]  Token não é armazenado em localStorage do app (apenas no webview)
- [ ]  Token não é logado em logs públicos
- [ ]  Validação de origem da requisição

**Performance**

- [ ]  Tempo de injeção do token < 100ms
- [ ]  Tempo total de checkout < 2 minutos (vs 3+ minutos sem SSO)
- [ ]  Nenhum impacto em performance do app

**Compatibilidade**

- [ ]  Funciona em Android 8+
- [ ]  Funciona em iOS 12+
- [ ]  Funciona em webview padrão
- [ ]  Funciona em navegadores customizados

**Instrumentação**

- [ ]  Evento de sucesso de autenticação é enviado
- [ ]  Evento de erro de autenticação é enviado
- [ ]  Tempo de checkout é registrado
- [ ]  Taxa de conversão é calculada corretamente

## 7. Instrumentação

**Eventos Necessários**

| Evento | Quando | Propriedades |
| --- | --- | --- |
| `sso_token_generated` | Token é gerado | user_id, timestamp |
| `sso_token_injected` | Token é injetado no webview | partner_id, timestamp |
| `sso_auth_success` | Autenticação automática funciona | partner_id, user_id, timestamp |
| `sso_auth_error` | Autenticação automática falha | partner_id, error_code, error_message, timestamp |
| `sso_login_skipped` | Usuário pula tela de login | partner_id, timestamp |
| `checkout_started` | Usuário inicia checkout | partner_id, user_id, timestamp |
| `checkout_completed` | Usuário completa checkout | partner_id, user_id, sale_amount, timestamp |
| `checkout_abandoned` | Usuário abandona checkout | partner_id, user_id, reason, timestamp |

**UTMs e Parâmetros**

| Parâmetro | Valor | Descrição |
| --- | --- | --- |
| `utm_source` | meliuz_app | Origem: app Méliuz |
| `utm_medium` | inapp_browser | Meio: In-App Browser |
| `utm_campaign` | sso_pre_auth_test | Campanha: teste SSO |
| `utm_content` | sso_v1 | Feature: SSO pré-autenticado |
| `mz_test_sso` | control / sso_preauth / data_prefill | Grupo do teste |
| `mz_sso_token` | <JWT> | Token de autenticação |
| `mz_sso_version` | 1.0 | Versão do SSO |

**Exemplo de Tracking URL**

```html
Plain Text

https://www.amazon.com.br/s?
  utm_source=meliuz_app&
  utm_medium=inapp_browser&
  utm_campaign=sso_pre_auth_test&
  utm_content=sso_v1&
  mz_test_sso=sso_preauth&
  mz_sso_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...&
  mz_sso_version=1.0&
  mz_redirect=inapp
```

**Validação de Instrumentação**

1. Teste em Staging
- Verificar que eventos são enviados corretamente
- Verificar que UTMs estão presentes
- Verificar que token é injetado
1. Teste em Produção (Canário )
- 1% do tráfego
- Monitorar erros
- Validar dados nos primeiros 24h
1. Checklist de Validação
- [ ]  Evento sso_token_generated é enviado
- [ ]  Evento sso_auth_success é enviado
- [ ]  UTM mz_test_sso está presente
- [ ]  Token é injetado no header HTTP
- [ ]  Taxa de conversão é calculada corretamente

## 8. Métricas de Sucesso e Guardrails

**Métrica Primária**

| Métrica | Critério | Justificativa |
| --- | --- | --- |
| Taxa de Conversão (CR) | ≥ 13,4% | Aumento de 0,5pp vs controle A (12,87%) |

Cálculo: CR = (Visitas com transação / Total de visitas) × 100

Significância Estatística: p-value < 0,05 (95% confiança)

**Métricas Secundárias**

| Métrica | Esperado | Justificativa |
| --- | --- | --- |
| Tempo de Checkout | Redução de 30% | Menos passos de autenticação |
| Taxa de abandono Login | Redução de 50% | Menos err |
|  |  |  |

**Guardrails**

| Métrica | Limite | Ação |
| --- | --- | --- |
| Taxa de Conversão | Não cair abaixo de 12,8% | Parar teste, investigar |
| Taxa de Erro | Não exceder 10% | Parar teste, investigar |
| Satisfação (NPS) | Não cair >5 pontos | Investigar feedback |
| Taxa de Abandono | Não aumentar >2pp | Investigar causa |

**Critérios de Decisão**

| Resultado | Decisão | Próximos Passos |
| --- | --- | --- |
| CR ≥ 13,4% + Guardrails OK | ✓ Sucesso | Implantar em 100% |
| CR 13,0-13,3% + Guardrails OK | ? Iteração | Testar com mais parceiros |
| CR < 13,0% ou Guardrail Falha | ✗ Fracasso | Investigar, redesenhar |

## 9. Riscos, Dependências e Fora de Escopo

**Riscos**

| Risco | Probabilidade | Impacto | Mitigação |
| --- | --- | --- | --- |
| Token interceptado | Baixa | Crítico | Criptografia, HTTPS, expiração curta |
| Parceiro não suporta | Média | Médio | Começar com top 10, fallback manual |
| Conversão não melhora | Média | Alto | Ter alternativa de pré-preenchimento |
| Impacto em NPS | Baixa | Médio | Monitorar feedback, rollback rápido |
| Erro de autenticação | Média | Médio | Logging detalhado, fallback para login manual |

**Dependências**

| Dependência | Proprietário | Status | Data |
| --- | --- | --- | --- |
| Aprovação de Backend | Tech Lead Backend | Pendente | Semana 1 |
| Integração com Parceiros | Partnerships | Pendente | Semana 2-3 |
| Validação de Segurança | Security Team | Pendente | Semana 1 |
| Infraestrutura de Teste | DevOps | Pronto | - |

**Fora de Escopo (V2+)**

1. Suporte a múltiplos provedores de identidade (apenas Google/Facebook em V1)
2. Sincronização de dados de perfil (endereço, telefone)
3. Logout automático
4. Biometria no webview
5. Integração com todos os 435 parceiros (apenas top 10 em V1)
6. Suporte a guest checkout
7. Integração com sistemas de pagamento

## 10. Pontos Adicionais Relevantes

**Alinhamento Estratégico**

1. Estratégia de Produto: aprofundar INAPP, não oferecer saída
2. OKR do Trimestre: aumentar conversão em 0,5pp
3. Visão de Longo Prazo: Melhorar experiência de checkout no app

**Aprendizados do Teste A/B/C**

1. Atrito Psicológico é Real: oferecer escolha prejudica conversão
2. Problema técnico existe: quem sai converte 1,4pp menos
3. Solução deve ser invisível: não oferecer confusão, apenas melhorar experiência

**Comunicação**

1.Stakeholders: Engenharia, Partnerships, Segurança, Analytics