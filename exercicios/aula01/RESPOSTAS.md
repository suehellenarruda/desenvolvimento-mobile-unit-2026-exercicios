EXERCÍCIO - AULA 01: O Ecossistema Mobile e o Lugar do Flutter.



Parte A - Análise de apps reais (A1 E A2):


APP: SPOTIFY

Item	| Resposta
Categoria:	Streaming de música e podcasts

A rolagem é fluida ou engasga?	R: Fluida — listas, carrossel de álbuns e transições entre telas ocorrem sem travamentos perceptíveis
A aparência é igual à de outros apps do Android?	R: Não — visual completamente customizado com tema escuro e verde característico, não segue o Material Design padrão do Android
Funciona em modo avião? O que ainda dá para fazer?	R: Sim, parcialmente. Reproduz músicas baixadas para offline, exibe a tela "Sem dados? Sem drama!" com acesso ao backup offline, mantém o player com controles de play/pause e fila. Não permite explorar catálogo online, buscar artistas que não estejam baixados ou acessar páginas de artista (mostra erro "Algo deu errado").
Abordagem que eu chuto:	React Native
Evidência do meu chute:	Post de engenharia do Spotify Engineering Blog documenta o uso de React Native em diversas telas do app. A presença de libhermes.so no APK (engine JS do React Native da Meta) confirma a abordagem.




APP: UBER

Item	| Resposta
Categoria:	Transporte / mobilidade urbana

A rolagem é fluida ou engasga? R:	Fluida — navegação entre telas rápida, mapa com renderização suave
A aparência é igual à de outros apps do Android? R:	Parcialmente — tem identidade visual própria com tema escuro, mas botões e navegação seguem influências do Material Design
Funciona em modo avião? O que ainda dá para fazer? R:	Parcialmente. Mantém a interface carregada com mapa em cache parcial, exibe endereços salvos ("House"), permite navegar entre telas e até ver opções de categorias de viagem em cache (UberX, Comfort). Exibe banner vermelho de erro de conexão e não consegue solicitar corridas nem atualizar preços.
Abordagem que eu chuto:	React Native
Evidência do meu chute:	O Uber Engineering Blog documentou adoção de React Native em partes do app de passageiros. A estrutura de telas, a forma como o app carrega conteúdo assincronamente e o padrão de degradação offline são consistentes com React Native.



APP: CITTAMOBI


Item | Resposta
Categoria:	Transporte público / mobilidade urbana (app brasileiro)
A rolagem é fluida ou engasga?	R: Fluida — listas de linhas e histórico de buscas rolam sem travamentos
A aparência é igual à de outros apps do Android?	R: Parcialmente — usa cards e barra de busca próximos ao Material Design, mas com identidade visual roxa própria e elementos muito customizados
Funciona em modo avião? O que ainda dá para fazer? R:	Funciona muito pouco. O mapa fica completamente em branco com fundo quadriculado (grid), exibe toast "Nenhum produto encontrado, tente novamente mais tarde". Apenas o histórico de linhas buscadas permanece visível. Rastreamento em tempo real e horários ficam totalmente indisponíveis.
Abordagem que eu chuto:	Flutter
Evidência do meu chute:	No screenshot do modo avião, o mapa exibe um fundo de grade branca quadriculada. Esse padrão é característico do motor de renderização do Flutter: quando tiles de mapa não carregam, o canvas próprio do Flutter aparece como fundo quadriculado, diferente do comportamento de apps nativos ou WebView. Esse padrão de retângulos é citado pela documentação e comunidade Flutter como um identificador visual da plataforma.



A3 — Teste do modo avião: qual app se comporta melhor?

O Spotify se comporta significativamente melhor sem rede.
Nos dois minutos de teste sem conexão, o Spotify foi o único dos três que continuou cumprindo sua função principal: reproduzir música. Ele exibiu proativamente a tela "Sem dados? Sem drama!" destacando o conteúdo disponível offline, permitiu buscar faixas baixadas ("Músicas disponíveis no modo off-line"), manteve o player completo funcionando e a música continuou tocando sem interrupções.
O que o Spotify faz de diferente é que ele implementa offline-first de forma intencional: antes de perder a conexão, o app já baixou e indexou o conteúdo marcado pelo usuário. Quando a rede cai, ele simplesmente serve o que está armazenado localmente, sem depender de nenhuma requisição de rede para a função central do produto.

O Uber manteve a interface carregada e o mapa com cache parcial, mas não conseguiu executar nenhuma ação real — pedir corrida é impossível sem rede, então sua degradação é mais elegante visualmente, mas igualmente paralisante para o usuário. 

O CittaMobi teve o pior comportamento: sem rede, o mapa ficou em branco e o app praticamente parou de funcionar, sem nenhuma estratégia de cache útil para o usuário.


Parte B — Escolhendo a abordagem:

B1 — Startup com 2 devs, 3 meses de prazo, Android e iOS. Abordagem: Flutter. 
Com equipe reduzida e prazo curto, manter dois codebases separados é inviável. Flutter permite que dois desenvolvedores entreguem um app funcional nas duas plataformas com um único codebase, hot reload acelerando o ciclo de feedback. O ecossistema de pacotes (pub.dev) reduz o tempo de implementação de funcionalidades comuns. O custo de manutenção a longo prazo também é menor com um único time.

B2 — App de edição de vídeo com filtros em tempo real. Abordagem: Nativo (Kotlin no Android / Swift no iOS). Processamento de vídeo frame a frame exige acesso direto às APIs de hardware: GPU via Vulkan/Metal, codecs de hardware via MediaCodec (Android) e AVFoundation (iOS), e pipelines de renderização de baixa latência. Flutter abstrai essa camada e o overhead do platform channel para transferência de frames de vídeo introduziria latência inaceitável em filtragem em tempo real. O desempenho é o fator determinante aqui, e nativo elimina qualquer intermediário.

B3 — Portal de notícias com site responsivo. Abordagem: WebView / Progressive Web App. 
O jornal já tem o produto pronto na web. Embrulhá-lo em um app com WebView — adicionando notificações push, ícone na loja e cache offline básico — entrega o resultado com fração do custo e prazo de qualquer desenvolvimento nativo ou Flutter. Qualquer atualização editorial reflete automaticamente, sem necessidade de novo build. O custo de manutenção a longo prazo é mínimo e a equipe existente de web já domina a stack.

B4 — App bancário com biometria e certificado no aparelho. Abordagem: Nativo (Kotlin + Swift). 
Armazenar certificados com segurança exige o Keystore do Android e o Secure Enclave do iOS — APIs de segurança de nível de SO que precisam de integração auditável e documentada. Reguladores como o Banco Central exigem que operações sensíveis sejam implementadas sobre garantias de hardware rastreáveis. Embora Flutter permita acessar essas APIs via plugins, a cadeia de auditoria de segurança fica mais complexa e qualquer gap em uma atualização de plugin pode criar vulnerabilidade. Em um app bancário, a responsabilidade regulatória justifica o nativo.

B5 — App interno, só Android, 200 funcionários sem internet. Abordagem: Nativo Android (Kotlin). 
Com plataforma única e requisito crítico de funcionamento offline robusto, o ecossistema nativo Android oferece Room (SQLite local), WorkManager para sincronização em background quando a rede retornar, e integração direta com MDM corporativo. Sem necessidade de suportar iOS, o ganho de cross-platform não existe. O custo de manutenção é baixo com Kotlin e o app pode ser distribuído via APK corporativo sem depender das lojas.

B6 — Jogo 2D casual com física e placar online. Abordagem: Flutter com Flame (ou Unity se a equipe tiver experiência). 
O engine Flame, construído sobre Flutter, oferece loop de game, física 2D, sprites e publicação em Android e iOS a partir de um único codebase — ideal para um jogo casual onde desempenho extremo não é requisito. Desenvolver nativo puro para um jogo 2D casual seria over-engineering sem ganho real. Se a equipe já conhece Unity, esta seria a alternativa com maior ecossistema de assets prontos.


Parte C — Pesquisa - Migrações Reais:

C1 — Dois casos de migração para Flutter:

Caso 1: BMW — My BMW App
A BMW utilizava antes uma solução híbrida baseada em Cordova/WebView para o app "My BMW", responsável pelo controle remoto do veículo (travar portas, verificar autonomia, pré-aquecer o carro). O problema central era a inconsistência visual entre Android e iOS e o desempenho insatisfatório do WebView para animações e interações em tempo real com o veículo. A BMW migrou para Flutter e apresentou o resultado no Flutter Engage 2021. Os ganhos relatados incluíram eliminação do time separado por plataforma (um único time Flutter), redução estimada de 30% no tempo de entrega de novas funcionalidades e melhora perceptível na fluidez das animações. A principal dificuldade foi integrar os SDKs proprietários do veículo (comunicação Bluetooth e ConnectedDrive) via platform channels, exigindo esforço adicional de engenharia de integração nativa.
Fonte: https://flutter.dev/showcase/bmw e palestra Flutter Engage 2021 (canal oficial Flutter no YouTube)

Caso 2: eBay Motors
O eBay Motors (divisão de veículos do eBay) utilizava apps nativos separados para Android e iOS, com times independentes e custo duplicado de manutenção. A migração para Flutter foi documentada pelo time de engenharia e apresentada como estudo de caso pela própria Google. Os ganhos relatados foram: redução de 50% no tamanho da equipe de desenvolvimento mobile (um time único em vez de dois), tempo de build significativamente menor e consistência de UI entre plataformas pela primeira vez. A dificuldade principal foi a curva de aprendizado do Dart para desenvolvedores que vinham de Kotlin e Swift, além da necessidade de reescrever componentes de UI customizados que existiam nas bases nativas legadas. 
Fonte: https://medium.com/ebay-tech-blog/ebay-motors-flutter-app e
Flutter Case Studies em https://flutter.dev/showcase.


C2 — Empresa que abandonou cross-platform e voltou ao nativo: Airbnb

Em 2018, o Airbnb publicou uma série de cinco artigos no Medium Engineering documentando a decisão de abandonar React Native e retornar ao desenvolvimento nativo em iOS (Swift) e Android (Kotlin).
Razões alegadas:
A razão central foi o JavaScript bridge — a camada de comunicação entre o código JavaScript e as APIs nativas que, à medida que o app crescia, tornava-se um gargalo de desempenho e uma fonte constante de bugs difíceis de reproduzir. Animações complexas e listas longas travavam porque o bridge não conseguia processar as atualizações de UI em 16ms por frame. Além disso, o Airbnb precisou manter um fork próprio do React Native para atender suas necessidades específicas, o que gerou custo de manutenção altíssimo acompanhando cada versão upstream. A necessidade de engenheiros que dominassem tanto React/JavaScript quanto as plataformas nativas também dificultou a contratação.
Essas razões se aplicariam ao Flutter?
Parcialmente. O Flutter elimina completamente o JavaScript bridge — esse era o maior problema do Airbnb — porque usa Dart compilado diretamente para código de máquina e se comunica com o nativo via platform channels, uma camada muito mais leve e previsível. O problema de desempenho em animações também não se aplica: o Flutter usa seu próprio motor de renderização (Skia/Impeller) independente de componentes nativos, garantindo 60/120fps consistentes. Porém, o desafio de manter integrações com funcionalidades profundas de plataforma via platform channels e a dificuldade de contratar profissionais com domínio de Dart ainda existem, em menor escala.

O que esse caso ensina:
Cross-platform não deve ser escolhido apenas para reduzir custo imediato. Quando o produto exige recursos de plataforma muito específicos, animações complexas ou integração intensa com o sistema operacional, o overhead de manutenção do framework pode superar o ganho de produtividade inicial. A decisão deve considerar o ciclo de vida do produto: um app que vai crescer e evoluir por anos exige que o framework escolhido acompanhe esse crescimento sem criar dívida técnica acumulada.
Fonte: https://medium.com/airbnb-engineering/react-native-at-airbnb-f95aa460be1c



Parte D — Preparação do Ambiente:

D3 - ## Bônus — flutter doctor -v

Saída completa obtida em 23/08/2026:

[√] Flutter 3.47.1 (channel stable) — instalado e funcionando.
    Dart 3.13.1, DevTools 2.60.0.

[!] Android toolchain (Android SDK 36.0.0) — SDK localizado em
    C:\Users\suear\AppData\Local\Android\sdk. Emulador 37.1.11.0
    instalado. Aviso de licença pendente: o Flutter 3.47.1 ainda
    consulta o sdkmanager clássico para verificar licenças, mas o
    Android SDK 36 descontinuou essa ferramenta em favor do novo
    Android CLI, que retorna "no longer needed" sem confirmar a
    aceitação. Trata-se de [!] aviso, não [X] erro bloqueante —
    o ambiente compila normalmente para Android.

[X] Visual Studio — não instalado. Necessário apenas para compilar
    apps Windows desktop, não afeta desenvolvimento mobile.

[√] Chrome — disponível para testes web (Chrome 151.0.7922.173).

[√] Connected device (3 disponíveis) — Windows desktop, Chrome
    e Edge prontos para teste.

[√] Network resources — conectividade com servidores Flutter OK.


---

## Declaração de uso de IA

Esta atividade foi desenvolvida com auxílio de assistente de IA (Claude/Inner AI)
para estruturação das respostas, diagnóstico do ambiente Flutter e orientação
no fluxo Git/GitHub. 
Todo o conteúdo foi revisado, compreendido e validado
por Sue Hellen Arruda com base nos testes reais realizados nos apps e no ambiente local.

