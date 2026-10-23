# ShoopBot Pro — App Android

Executa no seu celular as postagens enviadas pelo servidor (pasta `shoopbot-server`).
O servidor agenda (07:00, 09:00, 13:00, 17:00, 19:00, 21:00 ou 24 h) e cria a tarefa;
o app a busca a cada 15 s e usa o Serviço de Acessibilidade para tocar e digitar no app **Shopee**
(pacote `com.shopee.br`). O serviço só enxerga o app da Shopee.

## Estrutura
```
shoopbot-android/
├─ settings.gradle.kts · build.gradle.kts · gradle.properties
├─ gradle/wrapper/gradle-wrapper.properties
└─ app/
   ├─ build.gradle.kts
   └─ src/main/
      ├─ AndroidManifest.xml
      ├─ res/xml/accessibility_service_config.xml · res/values/strings.xml
      └─ java/com/shoopbot/pro/
         ├─ MainActivity.kt            (tela: servidor, token, iniciar/parar)
         ├─ PollService.kt             (busca tarefas, baixa o vídeo)
         ├─ BotAccessibilityService.kt (toques e digitação na Shopee)
         └─ Api.kt                     (conversa com o servidor)
```

## Gerar o .apk (Android Studio, o jeito mais fácil)
1. Instale o **Android Studio** (developer.android.com/studio).
2. `File → Open` e escolha a pasta `shoopbot-android`. Aguarde o "Sync" (ele baixa o Gradle 8.7 e o Android SDK 34; aceite as licenças).
3. Menu `Build → Build Bundle(s) / APK(s) → Build APK(s)`.
4. Clique em **locate** no aviso. O arquivo fica em:
   `app/build/outputs/apk/debug/app-debug.apk`
5. Envie o arquivo para o celular (cabo, Drive ou WhatsApp) e abra-o. Permita "instalar apps desconhecidos".

Pela linha de comando (JDK 17 + Android SDK instalados e `local.properties` com `sdk.dir=...`):
```bash
gradle wrapper --gradle-version 8.7   # uma vez só
./gradlew assembleDebug
```

## Configurar no celular
1. Abra o **ShoopBot Pro**, informe o endereço do servidor (`https://...`) e o `DEVICE_TOKEN`, toque em **Salvar e iniciar robô**.
2. **Android 13+:** Ajustes → Apps → ShoopBot Pro → menu ⋮ → **Permitir configurações restritas**. Sem isso o Android bloqueia a Acessibilidade em apps instalados fora da Play Store.
3. Toque em **1) Ativar Acessibilidade** → ShoopBot Pro → ligue o interruptor.
4. Toque em **2) Desativar economia de bateria** e deixe o app como "Sem restrições".
5. Mantenha o celular **ligado, desbloqueado e carregando** nos horários de postagem (Opções do desenvolvedor → "Manter ativo").
6. No painel, a Visão Geral deve mostrar "Celular: conectado".

## Calibrar (importante)
O fluxo do Shopee Vídeo depende dos textos exatos dos botões do app, que a Shopee pode mudar.
Se um passo falhar, o painel/log do servidor mostra: `Passo N (nome) falhou. Textos na tela: ...`.
Abra `BotAccessibilityService.kt`, função `steps()`, e ponha o texto que aparece na tela na lista `labels` do passo.
O passo "Escolher o vídeo mais recente" toca numa posição da tela (`0.17f, 0.30f`); ajuste se a grade da galeria for diferente.
O vídeo é salvo em `Movies/ShoopBot` e removido depois da postagem.

## Avisos
- Não foi testado em aparelho real nem na versão atual do app da Shopee: espere ajustar os rótulos.
- Automatizar o app da Shopee pode violar os termos deles e levar a bloqueio da conta. A decisão é sua.
- Para trafegar token e vídeo com segurança, use sempre HTTPS (o app bloqueia HTTP simples).
