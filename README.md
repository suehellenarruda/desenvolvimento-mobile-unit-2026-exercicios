# 📱 Exercícios — Desenvolvimento Mobile

**UNIT 2026** · Disciplina `F105880` · Prof. Petros Barreto
**Stack:** 100% Flutter + Dart · **80h** — 40 aulas de 2h

Este é o repositório **público** da disciplina. Aqui ficam as listas semanais,
a especificação do projeto **Checkpoint** e a validação automática dos Pull
Requests.

> Os **slides** ficam em repositório separado. O link é distribuído em sala e no
> ambiente virtual.

---

## 🚀 Começando (faça isso uma vez no semestre)

```bash
# 1. Faça FORK deste repositório (botão "Fork" no topo)

# 2. Clone o SEU fork
git clone https://github.com/SEU-USUARIO/desenvolvimento-mobile-unit-2026-exercicios.git
cd desenvolvimento-mobile-unit-2026-exercicios

# 3. Aponte para o repositório original, para receber as listas novas
git remote add upstream https://github.com/petrosbarreto/desenvolvimento-mobile-unit-2026-exercicios.git

# 4. Confira o ambiente Flutter (instalamos na Aula 02)
flutter --version      # esperado: 3.35.x (stable)
flutter doctor -v      # tudo verde, exceto iOS se você não usa macOS
```

### Antes de cada lista nova, atualize seu fork

```bash
git switch main
git pull upstream main
git push origin main
```

---

## 📤 Entregando uma lista

```bash
git switch main && git pull upstream main
git switch -c aula07

# resolva dentro de exercicios/aula07/

# rode ANTES de enviar
cd exercicios/aula07/<projeto_flutter>
flutter analyze          # sem erros
flutter test             # tudo verde

git add exercicios/aula07
git commit -m "Aula 07: coleções e funções em Dart"
git push -u origin aula07

# abra o Pull Request com o título EXATO:
#   [Aula 07] Seu Nome Completo
```

⚠️ **PR com título fora do padrão não é corrigido.** O formato é
`[Aula NN] Nome Completo` — colchetes, número com **dois dígitos**, nome como
consta na chamada. Para o projeto: `[Checkpoint M3] Nome Completo`.

### 🤖 Validação automática

Ao abrir o PR, o GitHub Actions roda:

| Verificação | Se falhar |
|---|---|
| `flutter analyze` nas pastas alteradas | ❌ comentário no PR com os avisos |
| `flutter test` | ❌ comentário com a saída dos testes |
| Presença de `RESPOSTAS.md` | ❌ comentário pedindo o arquivo |
| **Segredos commitados** (`google-services.json`, `*.jks`, `.env`) | ❌ **bloqueia o PR** |
| Título do PR no padrão | ⚠️ aviso |

O resultado sai em ~3 minutos. Corrija e dê **push no mesmo branch** — roda de
novo. Só corrijo manualmente PRs com o CI verde.

> 🔒 **Sobre segredos:** commitar `google-services.json`, keystore ou `.env`
> **zera a entrega**. Não é rigor acadêmico — é o erro que mais causa incidente
> de segurança real em equipes júnior.

---

## 📂 Estrutura de cada entrega

```
exercicios/aulaNN/
├── RESPOSTAS.md            # respostas escritas, prints, análises
├── <projeto_flutter>/      # o app da lista (quando houver código)
│   ├── lib/
│   ├── test/
│   └── pubspec.yaml
└── prints/                 # capturas de tela do app rodando
```

**Regras não negociáveis:**

1. `RESPOSTAS.md` com as seções nomeadas **como no enunciado** (`## Parte A`, `### A1`).
2. **Print do app rodando** em toda lista que tenha interface. Sem print, sem nota de interface.
3. `flutter analyze` **sem erros** (avisos são aceitáveis se justificados).
4. Código formatado: `dart format .` antes de commitar.
5. Nada de `print()` esquecido em código de produção — use `debugPrint` ou remova.

---

## 📊 Rubrica padrão de todas as listas

| Critério | Pontos | O que se avalia |
|---|---|---|
| **Faz o que foi pedido** | 40 | o app roda e cumpre o enunciado |
| **Qualidade do código** | 25 | organização, nomes, sem repetição, widgets extraídos |
| **Interface e experiência** | 20 | usável, estados de carregando/erro/vazio, responsivo |
| **Testes e robustez** | 15 | testes presentes, erros tratados, sem crash |

### Prazos

| | |
|---|---|
| Entrega | até a **véspera** da aula seguinte, 23h59 |
| Atraso de até 7 dias | −20 pontos |
| Atraso acima de 7 dias | não corrigido (conta como descartada) |

A nota de exercícios é a **média das 40 listas, descartando as 2 piores**.

---

## 🧮 Composição da nota final

| Instrumento | Peso |
|---|---|
| Listas semanais (40) | 30% |
| Marcos do projeto Checkpoint (M1–M6) | 30% |
| Seminário / resenha | 15% |
| Apresentação final do projeto (M7) | 25% |

---

## 🛠️ Projeto Integrador: **Checkpoint**

Um app **funcional e completo** de registro de visitas técnicas em campo,
construído das Aulas 12 a 40. Especificação em
[`projeto/README.md`](projeto/README.md).

```
🔐 Login                Firebase Authentication
📋 Minhas visitas       lista offline-first (SQLite local)
➕ Nova visita          formulário validado + foto + GPS
📡 Sincronização        REST API + MySQL quando há rede
👥 Feed da equipe       Cloud Firestore em tempo real
🔔 Notificações         push (FCM) ao receber nova visita
🎨 Interface            tema claro/escuro, responsivo
```

| Marco | Aula | Entrega | Peso |
|---|---|---|---|
| M1 | 18 | Navegação e telas com dados mock | 15% |
| M2 | 24 | Formulários validados + gerência de estado | 15% |
| M3 | 28 | Persistência local com SQLite | 20% |
| M4 | 32 | REST + MySQL, offline-first | 20% |
| M5 | 37 | Firebase: auth, tempo real, notificações | 15% |
| M6 | 39 | Sensores, câmera, GPS, APK assinado | 10% |
| M7 | 40 | Apresentação + APK funcional | 5% |

---

## 📚 Índice das listas

| Bloco | Aulas | Tema |
|---|---|---|
| **1** | [01](exercicios/aula01) · 02 · 03 · 04 · 05 | Ecossistema mobile, ambiente, adb, DevTools |
| **2** | 06 · 07 · 08 · 09 · 10 · 11 | Dart, OO, generics, assincronismo |
| **3** | 12 · 13 · 14 · 15 · 16 · 17 · 18 | Widgets, layout, Material 3, listas, navegação |
| **4** | 19 · 20 · 21 · 22 · 23 · 24 | Entrada de dados, estado, arquitetura |
| **5** | 25 · 26 · 27 · 28 · 29 | Arquivos, SharedPreferences, SQLite |
| **6** | 30 · 31 · 32 · 33 · 34 | HTTP, REST + MySQL, offline-first, JWT |
| **7** | 35 · 36 · 37 · 38 · 39 | Firebase, notificações, sensores, publicação |
| **8** | 40 | Projeto final |

As listas são publicadas **na semana da aula correspondente**.

---

## 📖 Bibliografia e referência rápida

| Recurso | Link |
|---|---|
| Documentação do Flutter | <https://docs.flutter.dev> |
| Documentação do Dart | <https://dart.dev/guides> |
| Catálogo de Widgets | <https://docs.flutter.dev/ui/widgets> |
| Effective Dart (estilo) | <https://dart.dev/effective-dart> |
| pub.dev (pacotes) | <https://pub.dev> |
| Material 3 | <https://m3.material.io> |
| Firebase + Flutter | <https://firebase.google.com/docs/flutter/setup> |

**Livros:** WINDMILL, E. *Flutter in Action* (Manning) ·
BIESSEK, A. *Flutter Apprentice* (Kodeco)

---

## 🤝 Colaboração e integridade

✅ **Permitido:** discutir ideias com colegas; consultar documentação, tutoriais
e Stack Overflow; usar assistentes de IA **desde que** você declare o uso no
`RESPOSTAS.md` e **entenda** cada linha entregue.

❌ **Não permitido:** copiar código de outro aluno; entregar código que você não
consegue explicar; commitar segredos.

> **Posso pedir que você explique qualquer linha da sua entrega.** Não conseguir
> explicar zera o item — independentemente de onde o código veio.

---

**Dúvidas?** Abra uma [Issue](../../issues) ou pergunte em sala.
