# Planejamento de Testes E2E com IA Generativa — React Native Crypto Alert

Este documento registra o processo de utilização de Inteligência Artificial Generativa como apoio ao levantamento de cenários e elaboração inicial de fluxos End-to-End (E2E) com **Maestro**, inspirado na metodologia do artigo *GenIA-E2ETest: A Generative AI-Based Approach for End-to-End Test Automation* (arXiv:2510.01024), seguido da revisão crítica e validação humana no código e no aplicativo em execução.

---

## 1. Prompt Utilizado

O prompt abaixo foi elaborado seguindo a ideia de contextualização funcional da aplicação e solicitação de cenários estruturados (nível de interpretação de requisitos) acompanhados de rascunhos de scripts para o Maestro (nível de geração de script alvo):

```text
Você é um especialista em Engenharia de Qualidade (QA) e automação de testes End-to-End (E2E) para aplicativos móveis React Native / Expo utilizando o framework Maestro.

Estou testando o aplicativo "Crypto Alert" (package/appId: com.anonymous.cryptoalert), que possui 3 abas principais na barra de navegação inferior:
1. "List" (tela inicial com título "Cryptocurrencies"): lista criptomoedas com nome, símbolo, preço atual em USD e variação de 24h. Quando há alertas cadastrados para uma criptomoeda, o card dela exibe um badge (ex.: "1 alert") que pode ser expandido ao tocar.
2. "Convert" (título "Convert Crypto"): permite digitar um valor ("Amount"), selecionar uma criptomoeda ("Cryptocurrency", padrão Bitcoin) e uma moeda fiduciária ("Fiat Currency", padrão USD, opções BRL, EUR, GBP, JPY) e exibe o valor convertido em "Converted Amount".
3. "Alerts" (título "Price Alerts"): lista os alertas de preço criados (ou mostra o estado vazio "No alerts yet" com botão "Create Alert" e botão "New Alert" no topo). Ao clicar em criar alerta, abre a tela "Create Alert" com seleção de criptomoeda ("Select cryptocurrency"), campo de preço alvo ("Target Price (USD)"), condição ("Above target price" ou "Below target price") e botão "Save Alert". Cada alerta salvo possui um botão de excluir.

Com base nessa descrição:
1. Proponha 6 cenários de teste E2E cobrindo fluxos principais, fluxos alternativos e casos de borda.
2. Para cada cenário, descreva: Objetivo, Pré-condições, Passos em linguagem natural e Resultado esperado.
3. Gere um rascunho inicial de script YAML do Maestro para os principais fluxos.
```

---

## 2. Resposta Original da IA

Abaixo está o registro integral da resposta gerada originalmente pela IA antes da revisão e validação prática no aplicativo:

```markdown
Aqui estão 6 cenários de teste E2E propostos para o aplicativo **Crypto Alert**, seguidos dos rascunhos em YAML para o Maestro:

### Cenário 1: Visualização e busca na lista de criptomoedas (Happy Path)
- **Objetivo:** Verificar se a tela inicial carrega a lista de criptomoedas e permite buscar uma moeda específica.
- **Pré-condições:** Aplicativo iniciado com conexão à internet.
- **Passos:**
  1. Abrir o aplicativo na aba "List".
  2. Verificar se o título "Cryptocurrencies" e as moedas "Bitcoin" e "Ethereum" estão visíveis.
  3. Digitar "Solana" no campo de busca "Search cryptocurrency".
  4. Verificar se apenas "Solana" aparece na listagem.
- **Resultado esperado:** A lista é filtrada exibindo o card da Solana com seu símbolo SOL e preço em USD.

### Cenário 2: Conversão de criptomoeda para moeda fiduciária (BTC para BRL)
- **Objetivo:** Validar a conversão de um valor em criptomoeda para Real Brasileiro (BRL).
- **Pré-condições:** Aplicativo aberto.
- **Passos:**
  1. Tocar na aba "Convert".
  2. Verificar o estado inicial com a mensagem "Enter an amount".
  3. Tocar no campo de valor ("0.00") e digitar "2".
  4. Selecionar a criptomoeda "Ethereum (ETH)" no seletor "Cryptocurrency".
  5. Selecionar a moeda fiduciária "BRL (R$)" no seletor "Fiat Currency".
  6. Tocar no botão "Convert".
- **Resultado esperado:** O painel "Converted Amount" exibe o valor convertido precedido pelo símbolo "R$".

### Cenário 3: Tentativa de criar alerta com campos vazios (Validação de erro de formulário)
- **Objetivo:** Verificar se o formulário impede a criação de um alerta sem preencher a criptomoeda e o preço alvo, exibindo mensagens de erro.
- **Pré-condições:** Usuário na tela "Create Alert".
- **Passos:**
  1. Navegar até a aba "Alerts" e tocar em "New Alert".
  2. Sem preencher nenhum campo, tocar diretamente no botão "Save Alert".
- **Resultado esperado:** O aplicativo permanece na tela "Create Alert" e exibe as mensagens "Please select a cryptocurrency" e "Please enter a target price".

### Cenário 4: Criação completa de alerta de preço (Acima e Abaixo do alvo) e reflexo na aba List
- **Objetivo:** Validar a criação de um alerta de preço para uma criptomoeda e verificar se ele aparece tanto na lista de alertas quanto no badge do card na aba "List".
- **Pré-condições:** Aplicativo iniciado com estado limpo.
- **Passos:**
  1. Ir para a aba "Alerts" e verificar o estado vazio ("No alerts yet").
  2. Tocar em "Create Alert".
  3. Tocar em "Select cryptocurrency" e escolher "Bitcoin".
  4. Digitar "75000" no campo de preço alvo.
  5. Selecionar a condição "Above target price".
  6. Tocar em "Save Alert".
  7. Verificar se o alerta de "Bitcoin" para "$75,000.00" aparece na tela "Price Alerts".
  8. Voltar para a aba "List", verificar o badge "1 alert" no card do Bitcoin e tocar nele para expandir os detalhes.
- **Resultado esperado:** O alerta é listado em "Price Alerts" e também exibido dentro do card expansível de Bitcoin na aba "List".

### Cenário 5: Exclusão de alerta de preço e retorno ao estado vazio
- **Objetivo:** Garantir que o usuário consiga remover um alerta existente e que a tela volte ao estado vazio quando não houver mais alertas.
- **Pré-condições:** Existir pelo menos um alerta cadastrado.
- **Passos:**
  1. Na aba "Alerts", localizar o alerta criado.
  2. Tocar no botão de lixeira ("delete-button") do card do alerta.
  3. Confirmar a exclusão no modal de confirmação Tocando em "Confirm".
- **Resultado esperado:** O alerta é removido e a mensagem "No alerts yet" volta a ser exibida.

### Cenário 6: Disparo de notificação push quando o preço atingir o alvo
- **Objetivo:** Validar se o sistema operacional exibe uma notificação push quando o preço do Bitcoin ultrapassa o valor configurado.
- **Pré-condições:** Alerta criado e permissão de notificação concedida.
- **Passos:**
  1. Criar um alerta para Bitcoin acima de $100.
  2. Aguardar a atualização do serviço em background e verificar a bandeja de notificações do Android.
- **Resultado esperado:** Uma notificação push é exibida no sistema.

---

### Rascunhos YAML gerados originalmente pela IA

#### Rascunho IA — Conversão:
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp
- tapOn: "Convert"
- assertVisible: "Convert Crypto"
- tapOn: "0.00"
- inputText: "2"
- tapOn: "Bitcoin (BTC)"
- tapOn: "Ethereum (ETH)"
- tapOn: "USD ($)"
- tapOn: "BRL (R$)"
- tapOn: "Convert"
- assertVisible: "R$ 39,426.56"
```

#### Rascunho IA — Criação e Exclusão de Alerta:
```yaml
appId: com.anonymous.cryptoalert
---
- launchApp
- tapOn: "Alerts"
- tapOn: "Create Alert"
- tapOn: "Select cryptocurrency"
- tapOn: "Bitcoin (BTC)"
- tapOn: "0.00"
- inputText: "75000"
- tapOn: "Above target price"
- tapOn: "Save Alert"
- assertVisible: "Bitcoin"
- assertVisible: "$75,000.00"
- tapOn:
    id: "delete-button"
- tapOn: "Confirm"
- assertVisible: "No alerts yet"
```
```

---

## 3. Cenários Selecionados (Após Revisão e Validação no Código/App)

Após inspecionar o código-fonte do projeto (`src/screens/*`, `src/components/*`, `src/data/cryptoData.ts`) e testar o aplicativo real no Android, foram selecionados e estruturados **4 fluxos E2E completos e determinísticos** para implementação na pasta `.maestro/`:

1. **Fluxo 01 (`01_listagem_e_navegacao_abas.yaml`) — Listagem de Criptomoedas e Navegação entre Abas:**
   - Valida o carregamento inicial da tela `"Cryptocurrencies"` (aguardando a resposta da API CoinGecko ou estado renderizado), verifica a presença de criptomoedas principais (`Bitcoin`, `BTC`, `Ethereum`, `ETH`), realiza rolagem na lista e valida a navegação completa entre as três abas inferiores (`List`, `Convert`, `Alerts`) e seus respectivos estados iniciais.
2. **Fluxo 02 (`02_conversao_criptomoedas.yaml`) — Conversão Reativa de Criptomoedas e Troca de Moedas (Crypto e Fiat):**
   - Valida o estado inicial (`"Enter an amount"`), a conversão reativa imediata ao digitar um valor no campo `"0.00"`, a abertura do modal customizado `SelectField` de `"Cryptocurrency"` (trocando de `Bitcoin (BTC)` para `Ethereum`), e a abertura do modal de `"Fiat Currency"` (trocando de `USD ($)` para `BRL` e `EUR`), validando a formatação do resultado (`R$`, `€`) sem depender de cotação estática fixa.
3. **Fluxo 03 (`03_criar_alerta_e_validar_badge_na_lista.yaml`) — Criação de Alertas (Acima e Abaixo), Validação de Estado do Formulário e Badge na Lista:**
   - Valida o estado vazio em `"Price Alerts"` (`"No alerts yet"`), navega para `"Create Alert"`, valida o botão `"Go back"` de cabeçalho, verifica a criação de um alerta com condição `"Above target price"` (ex.: `Bitcoin` a `$75,000.00`) e outro com condição `"Below target price"` (ex.: `Ethereum` a `$3,000.00`), e comprova a integração entre telas retornando à aba `"List"` para expandir o badge `"1 alert"` (`accessibilityLabel="Toggle alerts for this cryptocurrency"`) no card do Bitcoin e verificar o detalhe `"Above target"` / `"$75,000.00"`.
4. **Fluxo 04 (`04_excluir_alerta_e_estado_vazio.yaml`) — Ciclo Completo de Criação, Exclusão de Alerta e Retorno ao Estado Vazio:**
   - Com estado inicial limpo (`clearState: true`), cria um alerta de preço na aba `"Alerts"`, valida os dados exibidos no `AlertCard` (`Bitcoin`, `Alert when price goes above`, `$80,000.00`), aciona o botão de exclusão real (`accessibilityLabel="Delete alert"`) e comprova que a lista retorna imediatamente ao `EmptyState` (`"No alerts yet"` e botão `"Create Alert"`).

---

## 4. Análise Crítica: O Que Foi Aproveitado, Corrigido ou Descartado e Por Quê

A comparação entre a resposta original da IA e o código-fonte real do **React Native Crypto Alert** evidencia exatamente a tese do artigo *GenIA-E2ETest*: a IA generativa fornece uma excelente estrutura inicial, mas falha em detalhes de implementação de UI, seletores e regras implícitas de estado.

### O que foi APROVEITADO
- **A estrutura lógica das jornadas principais:** A divisão em listagem/navegação, conversão cambial, criação de alertas (com reflexo no badge da tela inicial) e exclusão de alertas com retorno ao estado vazio cobriu 100% das funcionalidades reais do app.
- **A sequência base de comandos YAML do Maestro:** O encadeamento `launchApp` → `tapOn` → `inputText` → `assertVisible` serviu como esqueleto produtivo, economizando tempo de escrita manual.
- **A ideia de testar a integração entre a tela de Alertas e a tela de Listagem (Cenário 4):** Validar que um alerta criado na aba `Alerts` faz surgir o badge `"1 alert"` no `CryptoCard` correspondente na aba `List` foi um excelente cenário E2E transversal.

### O que precisou ser CORRIGIDO (e por quê)
1. **Seletores do `SelectField` dentro do Modal (`Bitcoin (BTC)` no campo fechado vs. `Bitcoin` e `BTC` separados no Modal aberto):**
   - **Erro da IA:** No rascunho da IA, para escolher uma opção dentro do modal, ela usou `- tapOn: "Ethereum (ETH)"` e `- tapOn: "BRL (R$)"`.
   - **Causa no código (`src/components/SelectField/SelectField.tsx` e `useSelectField.ts`):** Quando o seletor está **fechado**, o componente concatena `displayLabel` como `"Bitcoin (BTC)"` ou `"USD ($)"`. Porém, quando o `Modal` abre, cada item da `FlatList` renderiza dois componentes `<Text>` separados: `<Text>{item.title}</Text>` (`"Ethereum"` / `"BRL"`) e `<Text>{item.subtitle}</Text>` (`"ETH"` / `"R$"`). Logo, `"Ethereum (ETH)"` não existe dentro do modal aberto!
   - **Correção:** Usar `- tapOn: "Bitcoin (BTC)"` para abrir o seletor fechado e `- tapOn: "Ethereum"` (ou `"BRL"`) para selecionar a opção no modal aberto.
2. **Inexistência de botão `"Convert"` na tela de conversão (Conversão Reativa):**
   - **Erro da IA:** No Cenário 2, a IA incluiu o passo `- tapOn: "Convert"` para disparar o cálculo e fez asserção em um valor fixo (`"R$ 39,426.56"`).
   - **Causa no código (`src/screens/ConversionScreen/useConversionScreen.ts`):** Não existe botão de submissão na tela de conversão; o cálculo roda automaticamente dentro de um `useEffect` assim que `amount`, `selectedCrypto` ou `selectedFiat` mudam. Além disso, tocar em `"Convert"` clicaria novamente no ícone da aba inferior! Por fim, como o app busca preços em tempo real da API da CoinGecko (`fetchCryptocurrenciesFromApi`), o valor exato varia a cada minuto.
   - **Correção:** Remover o clique no botão inexistente, fechar o teclado (`hideKeyboard`) após digitar a quantidade e utilizar expressão regular no Maestro (`assertVisible: "R\\$.*"` / `"\\$.*"`) para validar o cálculo dinâmico com resiliência.
3. **Seletor do botão de exclusão e ausência de modal de confirmação (`AlertCard.tsx`):**
   - **Erro da IA:** A IA alucinou um `id: "delete-button"` e um modal de confirmação com botão `"Confirm"`.
   - **Causa no código (`src/components/AlertCard/AlertCard.tsx`):** O botão de lixeira não possui `testID="delete-button"`, mas sim `accessibilityLabel="Delete alert"`, e a função `handleDelete` remove o item imediatamente do contexto/AsyncStorage sem abrir modal de confirmação.
   - **Correção:** Substituir por `- tapOn: "Delete alert"` e validar diretamente a transição para `"No alerts yet"`.
4. **Comportamento do botão `"Save Alert"` com campos vazios (`CreateAlertScreen.tsx`):**
   - **Erro da IA:** No Cenário 3, a IA propôs clicar em `"Save Alert"` com o formulário em branco para verificar as mensagens `"Please select a cryptocurrency"` e `"Please enter a target price"`.
   - **Causa no código (`src/screens/CreateAlertScreen/CreateAlertScreen.tsx`, linha 92):** Embora a função `validate()` exista no hook `useCreateAlertScreen.ts`, o componente renderiza `<PrimaryButton label="Save Alert" onPress={handleSave} disabled={!isFormValid} />`. Como `isFormValid` é `false` enquanto `selectedCrypto` ou `targetPrice` estão vazios, o `Pressable` fica nativamente **desabilitado** (`disabled={true}`), bloqueando o clique e impedindo que `handleSave` seja chamado com campos vazios.
   - **Correção:** Em vez de esperar mensagens de erro inalcançáveis ao clicar no botão desabilitado, validamos que o botão `"Save Alert"` permanece na tela (não submete o formulário incompleto) e só navega de volta para `"Price Alerts"` após ambos os campos válidos serem preenchidos.
5. **Fusão de ícones vetoriais (`@expo/vector-icons`) e rótulos em `accessibilityText` no Android e `accessibilityLabel` no badge da Home:**
   - **Erro da IA:** A IA gerou seletores exatos como `- tapOn: "Convert"`, `- tapOn: "Alerts"`, `- tapOn: "List"` e `- tapOn: "1 alert"`.
   - **Causa verificada na árvore real do Maestro (`screen-hierarchy`):** No React Native para Android, componentes `Pressable` e abas do `@react-navigation/bottom-tabs` agrupam seus filhos `<Text>` (incluindo o glifo Unicode do `Ionicons`) em um único atributo `accessibilityText` (por exemplo: `", List"`, `", Convert"`, `", Alerts"`, `"Bitcoin (BTC), ..."`). Além disso, no `CryptoCard.tsx`, o botão do badge `"1 alert"` define explicitamente `accessibilityLabel="Toggle alerts for this cryptocurrency"`, que sobrescreve o texto interno na árvore de acessibilidade do Android.
   - **Correção:** Ajustar os seletores no Maestro para expressões regulares compatíveis com a árvore de acessibilidade real (`".*Convert"`, `".*Alerts"`, `".*List"`, `".*Bitcoin \\(BTC\\).*"`) e usar `"Toggle alerts for this cryptocurrency"` para expandir o badge na tela inicial.

### O que foi DESCARTADO (e por quê)
1. **Busca por texto na tela de listagem (parte do Cenário 1 da IA):**
   - **Motivo do descarte:** A IA alucinou a existência de uma barra de pesquisa (`"Search cryptocurrency"`), provavelmente induzida pelo ícone `search` usado apenas no componente `EmptyState` (`CryptoListScreen.tsx`, linha 32). A tela `CryptoListScreen` exibe apenas o título `"Cryptocurrencies"` e a `FlatList` de cards.
2. **Disparo de notificação push em background (Cenário 6 da IA):**
   - **Motivo do descarte:** Apesar do nome do projeto ser *Crypto Alert*, a análise de `package.json` e `src/context/AlertProvider/useAlertProvider.ts` mostra que o app persiste os alertas localmente via `@react-native-async-storage/async-storage` e os exibe na aba `Alerts` e nos cards da aba `List`, **não possuindo** integração com `expo-notifications` ou *background workers*. Esse é um exemplo clássico de alucinação funcional baseada apenas no nome do produto.
