# 📱 Projeto Integrador: **Checkpoint**

**Disciplina:** Desenvolvimento Mobile (`F105880`) — UNIT 2026
**Professor:** Petros Barreto
**Stack:** 100% Flutter + Dart
**Peso:** 55% da média final (30% marcos + 25% apresentação)
**Entrega final:** Aula 40 — apresentação de 15 min + **APK funcional instalado**

---

## 🎯 O que você vai construir

**Checkpoint** é um aplicativo de **registro de visitas técnicas em campo**.
Um técnico recebe visitas atribuídas, vai até o local (muitas vezes **sem
internet**), registra o que encontrou com foto e localização, e o app sincroniza
quando a rede volta.

O escopo foi desenhado para exercitar **todos** os tópicos da ementa. Nada aqui
é decorativo — cada funcionalidade existe porque cobre um item do plano de
ensino.

```
┌──────────────────────────────────────────────────────────────┐
│  CHECKPOINT                                                  │
│                                                              │
│  🔐 Login              Firebase Authentication               │
│  📋 Minhas visitas     Lista offline-first (SQLite local)    │
│  ➕ Nova visita        Formulário validado + foto + GPS      │
│  📡 Sincronização      REST API + MySQL quando há rede       │
│  👥 Feed da equipe     Cloud Firestore em tempo real         │
│  🔔 Notificações       FCM push ao receber nova atribuição   │
│  🎨 Tema e layout      Claro/escuro, responsivo (fone/tablet)│
└──────────────────────────────────────────────────────────────┘
```

---

## 🗺️ Cobertura da ementa

| Funcionalidade do Checkpoint | Item da ementa | Aula |
|---|---|---|
| Widgets, telas e navegação | Interface do usuário: arquitetura e recursos | 12–18 |
| Formulário de visita com validação | Controles de entrada, interação do usuário | 19–21 |
| Gerência de estado e camadas | Fundamentos, OO, arquitetura | 22–24 |
| Anexos e cache de fotos | Sistema de arquivos | 25 |
| Banco local de visitas | **SQLite Database** | 27–28 |
| Sincronização com o servidor | **Acesso a rede + servidor MySQL** | 30–32 |
| Login e sessão | Serviços em nuvem | 33, 35 |
| Feed em tempo real | **Firebase Realtime / Firestore** | 36 |
| Aviso de nova visita atribuída | **Serviços receptores e notificações** | 37 |
| Foto, GPS e leitura de sensores | **Sensores e Touch nativo** | 38 |
| Instalar e depurar no aparelho | **Android Debug Bridge** | 04, 39 |

---

## 📅 Marcos e cronograma

Cada marco é um **Pull Request** com o título `[Checkpoint MN] Seu Nome`.

| Marco | Aula | Entrega | Peso |
|---|---|---|---|
| **M1** | 18 | Navegação e telas com **dados mock**: login, lista, detalhe, formulário. Rotas funcionando, tema aplicado, layout responsivo. | 15% |
| **M2** | 24 | **Formulários validados** + gerência de estado com Provider + separação em camadas (model, repository, viewmodel, ui). | 15% |
| **M3** | 28 | **Persistência local com SQLite**: CRUD completo de visitas, migrations, DAO testado. O app funciona 100% offline. | 20% |
| **M4** | 32 | **Integração REST + MySQL** com estratégia **offline-first**: fila de sincronização, resolução de conflito, indicador de estado. | 20% |
| **M5** | 37 | **Firebase**: login real, feed em tempo real da equipe, push notification ao receber visita. | 15% |
| **M6** | 39 | **Recursos nativos**: câmera, GPS, sensor. **APK assinado** gerado e instalado via `adb`. | 10% |
| **M7** | 40 | **Apresentação** (15 min) + demonstração ao vivo + repositório documentado. | 5% |

> ⚠️ **Os marcos são cumulativos.** M3 exige que M1 e M2 continuem funcionando.
> Regressão em marco anterior desconta na nota do marco atual.

---

## 🧱 Arquitetura exigida (a partir do M2)

```
lib/
├── main.dart
├── app.dart                       # MaterialApp, tema, rotas
│
├── core/
│   ├── theme/                     # ThemeData claro e escuro
│   ├── routes/                    # go_router / Navigator
│   ├── network/                   # cliente HTTP, interceptors
│   └── erros/                     # exceptions e Result/Either
│
├── data/
│   ├── local/
│   │   ├── database.dart          # abertura do SQLite, migrations
│   │   └── visita_dao.dart        # CRUD
│   ├── remote/
│   │   └── visita_api.dart        # chamadas REST
│   └── repositories/
│       └── visita_repository.dart # decide local x remoto, sincroniza
│
├── domain/
│   └── models/
│       ├── visita.dart            # entidade + toMap/fromMap + toJson/fromJson
│       └── usuario.dart
│
└── ui/
    ├── login/
    ├── visitas/                   # lista
    ├── visita_detalhe/
    ├── visita_form/
    └── widgets/                   # componentes reutilizáveis
```

**A regra que vale nota:** a camada `ui/` **nunca** chama `sqflite` ou `http`
diretamente. Ela conversa com o **repository**, e só. Essa separação é o que
torna o app testável — e é o que vou verificar na correção.

---

## 🗄️ Modelo de dados

### Tabela local (SQLite)

```sql
CREATE TABLE visitas (
  id             TEXT PRIMARY KEY,          -- uuid gerado no cliente
  titulo         TEXT    NOT NULL,
  cliente        TEXT    NOT NULL,
  endereco       TEXT    NOT NULL,
  status         TEXT    NOT NULL,          -- agendada|em_andamento|concluida
  agendada_para  INTEGER NOT NULL,          -- epoch millis
  observacoes    TEXT,
  latitude       REAL,
  longitude      REAL,
  foto_path      TEXT,                      -- caminho local do arquivo
  criada_em      INTEGER NOT NULL,
  atualizada_em  INTEGER NOT NULL,
  sincronizada   INTEGER NOT NULL DEFAULT 0 -- 0 = pendente, 1 = enviada
);

CREATE INDEX idx_visitas_status       ON visitas(status);
CREATE INDEX idx_visitas_sincronizada ON visitas(sincronizada);
```

### API REST (fornecida pelo professor, sobre MySQL 8)

| Método | Rota | Descrição |
|---|---|---|
| `POST` | `/auth/login` | devolve JWT |
| `GET` | `/visitas?desde=<epoch>` | visitas do técnico, alteradas desde |
| `POST` | `/visitas` | cria (idempotente pelo `id` do cliente) |
| `PUT` | `/visitas/{id}` | atualiza |
| `POST` | `/visitas/{id}/foto` | upload multipart |

Base URL e credenciais de teste são distribuídas na Aula 31.

---

## 🔄 A regra de sincronização (M4)

Este é o ponto mais difícil do projeto, e o mais valioso profissionalmente.

```
AO ABRIR O APP ou AO VOLTAR A REDE:

1. ENVIAR pendências
   para cada visita com sincronizada = 0:
       POST/PUT na API
       se sucesso → marca sincronizada = 1
       se falha   → mantém na fila, tenta de novo depois

2. BAIXAR novidades
   GET /visitas?desde=<ultima_sincronizacao>
   para cada visita recebida:
       se não existe localmente        → insere
       se existe e local está sincronizada → sobrescreve
       se existe e local está PENDENTE → CONFLITO

3. RESOLVER CONFLITO
   Estratégia mínima aceita: "o cliente vence" (last-write-wins local),
   registrando o evento em log.
   Estratégia para nota máxima: apresentar a divergência ao usuário
   e deixá-lo escolher.
```

**O app deve mostrar sempre**, de forma visível: quantas visitas estão
pendentes de sincronização e quando foi a última sincronização bem-sucedida.

---

## 🎚️ Níveis de escopo

Escolha o seu nível **na Aula 12** e declare no `README.md` do seu projeto.

| Nível | O que entrega | Nota máxima |
|---|---|---|
| **1 — Essencial** | Todos os marcos M1–M6 com o escopo mínimo descrito acima | 8,5 |
| **2 — Completo** | + testes de widget e de integração, tema claro/escuro persistido, tratamento de permissões negadas, estados de erro e vazio em todas as telas | 10,0 |
| **3 — Avançado** | + resolução interativa de conflito, busca e filtros, exportação de relatório em PDF, animações de transição, CI no GitHub Actions rodando `flutter test` | 10,0 + bônus |

**Bônus adicionais (+0,25 cada, máx. +1,5):**

- Modo escuro que segue o sistema **e** pode ser sobrescrito
- Internacionalização (pt-BR e en) com `flutter_localizations`
- Acessibilidade: `Semantics`, contraste AA, navegação por leitor de tela
- Widget de resumo na tela inicial do Android (*home screen widget*)
- Deep link que abre uma visita específica
- `flutter build web` funcionando com fallback quando não há sensores

---

## ✅ Critérios de avaliação de cada marco

| Critério | Pontos |
|---|---|
| **Funciona** — roda no aparelho e faz o que o marco pede | 40 |
| **Arquitetura** — camadas respeitadas, UI não acessa dados direto | 25 |
| **Interface** — usável, estados de carregamento/erro/vazio, responsivo | 20 |
| **Testes e robustez** — testes presentes, erros tratados, sem crash | 15 |

### O que **zera** um marco

- App não compila (`flutter build apk --debug` falha)
- Crash na primeira tela
- Credenciais, chaves de API ou `google-services.json` **commitados**
- Código que você não consegue explicar

---

## 🚀 Setup do projeto

```bash
flutter create --org br.edu.unit.checkpoint --platforms=android,ios checkpoint
cd checkpoint

flutter pub add provider go_router http dio sqflite path path_provider \
                shared_preferences flutter_secure_storage intl uuid \
                connectivity_plus image_picker geolocator sensors_plus \
                permission_handler cached_network_image

flutter pub add --dev flutter_lints mocktail integration_test

# Firebase entra na Aula 35
# flutter pub add firebase_core firebase_auth cloud_firestore firebase_messaging

flutter run
```

### `.gitignore` obrigatório

```gitignore
# NUNCA commitar
google-services.json
GoogleService-Info.plist
*.jks
*.keystore
key.properties
.env
lib/core/config/secrets.dart
```

> ⚠️ Use `--dart-define` ou um `secrets.dart.example` versionado. Chave commitada
> zera o marco — e, na vida real, vaza sua conta.

---

## 📄 Entrega final (Aula 40)

### Apresentação — 15 minutos

| Tempo | O quê |
|---|---|
| 2 min | O problema que o app resolve e para quem |
| 8 min | **Demonstração ao vivo no aparelho** — inclusive em modo avião, mostrando a sincronização ao religar a rede |
| 3 min | Arquitetura: uma tela mostrando as camadas e por que essa organização |
| 2 min | O que não funciona, o que você faria diferente, e perguntas |

### Repositório

```
checkpoint/
├── README.md          # o que é, nível escolhido, como rodar, prints
├── ARQUITETURA.md     # diagrama de camadas e decisões
├── lib/
├── test/
└── build/app/outputs/flutter-apk/app-release.apk    # ou link
```

O `README.md` precisa conter: **prints de todas as telas**, o passo a passo para
rodar do zero, e a lista honesta de limitações conhecidas.

---

## 💡 Dicas de quem já corrigiu muitos desses

**Comece pelo M3 mentalmente.** O modelo de dados que você define no M1 vai doer
ou salvar no M3 e M4. Pense em `id`, `atualizada_em` e `sincronizada` desde o
primeiro dia — mesmo com dados mock.

**Offline-first não é "tratar erro de rede".** É projetar assumindo que a rede
**não existe** e que ela é um bônus quando aparece. A fonte de verdade do app é o
SQLite local, sempre.

**Teste em modo avião desde o M1.** Se o app trava sem internet, você descobre em
maio, não em novembro.

**Não deixe o Firebase para o fim.** A configuração (SHA-1, `google-services.json`,
regras de segurança) costuma consumir uma tarde inteira na primeira vez.

---

**Dúvidas?** Abra uma Issue no repositório de exercícios ou pergunte em sala.
