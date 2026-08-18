# Exercício — Aula 05: DevTools e o Ciclo de Desenvolvimento

**Disciplina:** Desenvolvimento Mobile (`F105880`) — UNIT 2026
**Entrega:** até a véspera da Aula 06 — PR `[Aula 05] Seu Nome Completo`

> ⚠️ **Esta lista usa o `checkpoint-app`**, o repositório do projeto do semestre.
> Faça o fork agora — a partir da Aula 12 ele passa a ser o seu projeto.
>
> ```bash
> # 1. Faça FORK de https://github.com/petrosbarreto/checkpoint-app
> git clone https://github.com/SEU-USUARIO/checkpoint-app.git
> cd checkpoint-app && flutter pub get && flutter run
> ```
>
> Login: qualquer e-mail válido, senha com 6+ caracteres.

---

## Parte A — Flutter Inspector (25 pontos)

**A1 (8 pts) — Mapeando a árvore viva.**

Abra as DevTools → Inspector → ative o **Select Widget Mode** e toque em cada
elemento. Anote o widget selecionado e **o caminho na árvore** (os 3 ancestrais
mais próximos):

| Toque em | Widget | Ancestrais |
|---|---|---|
| título de um cartão | | |
| faixa colorida de status | | |
| ícone de nuvem (pendente) | | |
| FAB "Nova visita" | | |
| banner "aguardando envio" | | |

**A2 (10 pts) — Provocando o overflow.**

Em `lib/ui/widgets/visita_card.dart`, **remova o `Expanded`** que envolve o
`Text` da data. Em `lib/data/repositories/visitas_mock.dart`, troque um endereço
por um texto de ~120 caracteres.

Registre:

(a) a mensagem **completa** do console — quantos pixels transbordaram?
(b) o arquivo e a linha apontados;
(c) print do **Layout Explorer** mostrando a distribuição da `Row`;
(d) o print da tela com a faixa listrada.

**A3 (7 pts) — Três correções.**

Aplique e teste as três, com print de cada uma:

```dart
Expanded(child: Text(...))
Flexible(child: Text(...))
Expanded(child: Text(..., maxLines: 1, overflow: TextOverflow.ellipsis))
```

**Qual é a melhor para este caso?** Justifique em 3 linhas, considerando o que
acontece quando o endereço é curto e quando é longo.

---

## Parte B — Depuração com breakpoints (25 pontos)

**B1 (10 pts) — Breakpoint simples.**

Ponha um breakpoint na primeira linha de `_salvar`, em
`lib/ui/visita_form/visita_form_page.dart`. Rode em **debug** (F5), crie uma
visita e toque em "Salvar visita".

Com a execução parada, registre (com print do painel Variables):

| Variável | Valor |
|---|---|
| `_tituloCtrl.text` | |
| `_status` | |
| `widget.editando` | |
| `_agendadaPara` | |

E o resultado destas expressões avaliadas no **Debug Console**:

```dart
_tituloCtrl.text.length
_agendadaPara.isAfter(DateTime.now())
_obsCtrl.text.isEmpty
StatusVisita.values.map((s) => s.rotulo).toList()
```

**B2 (10 pts) — Breakpoint condicional.**

Em `lib/data/repositories/visita_repository_memoria.dart`, ponha um breakpoint
**condicional** no método `salvar` com a condição `!visita.sincronizada`.

Rode, crie duas visitas e altere o status de uma existente.

(a) Quantas vezes ele parou?
(b) Print do **Call Stack** numa das paradas. Quem chamou `salvar`? E quem
chamou aquele?
(c) A condição para em **toda** chamada de `salvar`. Por quê? *(Leia o corpo do
método — há um `copyWith` ali.)*
(d) Escreva uma condição que pare **só** quando o título contiver a palavra
"teste". Mostre que funciona.

**B3 (5 pts) — A comparação.**

Para responder B2(b) usando `print`, quantas linhas você teria que escrever, em
quantos arquivos? Escreva sua conclusão sobre breakpoint x `print` em 3 linhas.

---

## Parte C — Debug x profile, medido (20 pontos)

**C1 (12 pts).** Rode o app nos dois modos e faça **exatamente** a mesma
navegação (entrar, rolar a lista rapidamente 5 vezes, abrir um detalhe, voltar):

```bash
flutter run              # debug
flutter run --profile    # profile
```

Abra a aba **Performance** em cada um e registre:

| | debug | profile |
|---|---|---|
| Quadros acima de 16,7 ms | | |
| Pior quadro (ms) | | |
| Print do gráfico | | |

**C2 (8 pts) — Lista grande.**

Em `lib/app.dart`, troque por uma lista de 500 visitas:

```dart
VisitaRepositoryMemoria(
  iniciais: List.generate(
    500,
    (i) => visitasMock()[i % 5].copyWith(titulo: 'Visita numero $i'),
  ),
)
```

⚠️ Os ids vão repetir e o `Map` interno vai deduplicar. Para ter 500 de verdade,
gere ids novos com `Uuid().v4()` — descreva no `RESPOSTAS.md` o que você fez.

Rode em `--profile` e role rapidamente. Responda:

(a) A rolagem manteve 60 fps? Cole o gráfico.
(b) **Por que** o `ListView.builder` dá conta de 500 itens? O que aconteceria com
`ListView(children: [...])`? *(Leia o comentário em `visitas_page.dart`.)*
(c) Se um quadro estourasse, você olharia a barra **azul** ou **verde** primeiro?
O que cada uma indicaria?

---

## Parte D — Caçando um vazamento de memória (20 pontos)

**D1 (12 pts).** Em `lib/ui/visita_form/visita_form_page.dart`, **comente todo o
método `dispose`**.

Rode, abra a aba **Memory**, e abra/feche o formulário **15 vezes**, clicando no
botão de coleta de lixo (🗑) entre as idas.

Registre o print do gráfico e responda: o consumo voltou ao patamar inicial?

**D2 (8 pts).** **Descomente** o `dispose` e repita exatamente o mesmo
procedimento. Cole o segundo gráfico e compare.

Depois responda:

(a) Se o Dart tem coletor de lixo, por que o `TextEditingController` não é
liberado sozinho?
(b) Faça um inventário: quais objetos do `checkpoint-app` precisam de `dispose`
ou `cancel`? Procure em todo o `lib/` e liste arquivo por arquivo.
(c) Por que `super.dispose()` tem que ser a **última** linha do método?

---

## Parte E — Bônus: acessibilidade (+10 pontos)

**E1.** Ative a flag **Show Semantics** no Inspector e navegue pelo app.

(a) Print da árvore de semântica na tela da lista.
(b) Existe algum elemento **interativo** que o leitor de tela não anuncia
corretamente? Qual?
(c) O ícone de nuvem (pendente de sincronização) é anunciado? Ele tem `Tooltip` —
isso é suficiente para acessibilidade?
(d) A faixa de status comunica a informação **só por cor**? Verifique no código e
explique por que isso importa para quem tem daltonismo.
(e) Escolha **um** problema que você encontrou e corrija com `Semantics(...)`.
Mostre o antes e o depois na árvore.

---

## Entrega

```
exercicios/aula05/
├── RESPOSTAS.md          # Partes A-E
├── patches/              # os trechos de código que você alterou
│   ├── a2-sem-expanded.dart
│   ├── a3-tres-correcoes.dart
│   └── e5-semantics.dart
└── prints/
    ├── a1-inspector.png
    ├── a2-overflow.png · a2-layout-explorer.png
    ├── b1-variables.png · b2-call-stack.png
    ├── c1-debug.png · c1-profile.png · c2-500-itens.png
    ├── d1-vazamento.png · d2-com-dispose.png
    └── e1-semantics.png
```

⚠️ **Não** commite o `checkpoint-app` inteiro nesta pasta. Commite apenas o
`RESPOSTAS.md`, os `patches/` e os `prints/` — o app é um repositório à parte.

## Rubrica

| Critério | Pontos |
|---|---|
| Faz o que foi pedido | 40 |
| Qualidade do diagnóstico (você explicou a **causa**, não só o sintoma) | 25 |
| Evidências (prints legíveis, mensagens completas) | 20 |
| Cuidado com detalhes | 15 |
| **Bônus acessibilidade** | +10 |

---

## 🏁 Fim do Bloco 1

Com esta lista você fecha o primeiro bloco: ecossistema, ambiente, `adb` e
DevTools. Você sabe **rodar** e **diagnosticar** — e essa é a base de tudo o que
vem depois.

**A Aula 06 abre o Bloco 2: Dart.** Abra o <https://dartpad.dev> antes da aula.
