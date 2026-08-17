# Exercício — Aula 03: Anatomia de um Projeto Flutter

**Disciplina:** Desenvolvimento Mobile (`F105880`) — UNIT 2026
**Entrega:** até a véspera da Aula 04 — PR `[Aula 03] Seu Nome Completo`

> Crie um projeto novo para esta lista:
> ```bash
> flutter create --org br.edu.unit --platforms=android anatomia_app
> ```

---

## Parte A — Anatomia comentada (20 pontos)

**A1 (12 pts).** No `lib/main.dart` do `anatomia_app`, adicione um comentário
explicando **com suas palavras** cada uma das linhas abaixo. Um comentário
copiado do slide vale metade.

```dart
void main()
runApp(const MyApp())
class MyApp extends StatelessWidget
Widget build(BuildContext context)
return MaterialApp(...)
home: const MyHomePage(...)
class MyHomePage extends StatefulWidget
final String title
State<MyHomePage> createState()
class _MyHomePageState extends State<MyHomePage>
int _counter = 0
setState(() { _counter++; })
appBar: AppBar(title: Text(widget.title))
onPressed: _incrementCounter
```

**A2 (8 pts).** Responda no `RESPOSTAS.md`:

1. Por que um `StatefulWidget` precisa de **duas** classes em vez de uma?
2. Por que `_counter` fica na classe de estado e `title` na classe do widget?
3. O que o `_` no início de `_MyHomePageState` e `_counter` significa em Dart?
4. Quantas vezes o `build()` é chamado? Que tipo de código **nunca** deve estar
   dentro dele, e por quê?

---

## Parte B — Catálogo de erros (25 pontos)

Provoque **cada** erro abaixo, leia a mensagem **inteira** e registre.

| # | O que fazer | Pts |
|---|---|---|
| B1 | `onPressed: _incrementCounter()` (com parênteses) | 4 |
| B2 | Tirar o `setState`, deixando só `_counter++` | 5 |
| B3 | `Text(_counter)` em vez de `Text('$_counter')` | 4 |
| B4 | `children: [...]` dentro de um `Center` | 4 |
| B5 | `Row` com um `Text` de 200 caracteres, sem `Expanded` | 4 |
| B6 | Trocar o `Scaffold` por `Center` direto no `home:` | 4 |

**Formato do registro** (para cada um):

```markdown
### B1 — onPressed com parênteses

**Mensagem completa (copiada):**
```
The argument type 'void' can't be assigned to the parameter type
'void Function()?'.
```

**Onde apareceu:** lib/main.dart, linha 87 — no editor, antes de rodar

**O que a mensagem está dizendo, com minhas palavras:**
...

**Como corrigi:**
...
```

⚠️ **O B2 é o mais importante da lista.** Ele **não gera erro nenhum** — compila
e roda. Responda: por que esse tipo de bug é mais perigoso que um erro de
compilação? Como você o detectaria antes de o usuário reclamar?

---

## Parte C — Criando widgets (35 pontos)

**C1 (10 pts) — `CartaoAluno` (Stateless).**
Crie `lib/widgets/cartao_aluno.dart` com um widget que recebe `nome`, `curso` e
`periodo` (opcional, padrão 5) e exibe um `Card` com avatar circular (primeira
letra do nome), nome em destaque e `curso — Nº período`.

Requisitos: construtor `const`, campos `final`, `required` nos obrigatórios,
usar `Theme.of(context)` para o estilo do texto (nada de cor ou tamanho fixos).

**C2 (10 pts) — `Cronometro` (Stateless).**
Crie um widget que recebe `int segundos` e exibe formatado como `MM:SS`.
Ex.: `125` → `02:05`.

Dica: `(segundos ~/ 60).toString().padLeft(2, '0')`. O `~/` é divisão inteira.

**C3 (15 pts) — `ContadorDeCliques` (Stateful).**
Um widget com:
- um número grande no centro;
- botão `+` que incrementa e botão `−` que decrementa;
- o número **nunca fica negativo** (o `−` não faz nada em 0);
- a cor do número muda: cinza em 0, azul de 1 a 9, verde de 10 em diante;
- um botão de texto "Zerar".

**Responda no `RESPOSTAS.md`:** por que este widget **precisa** ser Stateful?
Qual critério da aula se aplica?

### Montando tudo

O `body:` do `MyHomePage` deve exibir os três widgets: um `ContadorDeCliques`,
um `Cronometro` e uma lista com pelo menos 3 `CartaoAluno`.

⚠️ **Requisitos de qualidade:** `flutter analyze` sem erros, `dart format .`
aplicado, cada widget em seu próprio arquivo dentro de `lib/widgets/`.

**Prints obrigatórios:** um por widget, mais um da tela montada.

---

## Parte D — Árvore de widgets (20 pontos)

**D1 (12 pts).** Desenhe (em ASCII, no `RESPOSTAS.md`) a árvore de widgets do
código abaixo. Use o formato com `├──` e `└──` da aula.

```dart
Scaffold(
  appBar: AppBar(
    title: const Text('Perfil'),
    actions: [IconButton(icon: const Icon(Icons.edit), onPressed: null)],
  ),
  body: SingleChildScrollView(
    child: Column(
      children: [
        const SizedBox(height: 24),
        const CircleAvatar(radius: 48, child: Icon(Icons.person, size: 48)),
        const SizedBox(height: 16),
        Text('Ana Souza', style: Theme.of(context).textTheme.headlineSmall),
        const Text('Sistemas de Informação'),
        const Divider(),
        Padding(
          padding: const EdgeInsets.all(16),
          child: Row(
            children: const [
              Expanded(child: Text('Período')),
              Text('5º'),
            ],
          ),
        ),
      ],
    ),
  ),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.share),
  ),
)
```

**D2 (8 pts).** Responda sobre essa árvore:

(a) Quais widgets recebem `child:` (um filho) e quais recebem `children:` (lista)?
(b) Qual a função do `Expanded` dentro daquela `Row`? O que aconteceria sem ele
se o texto fosse muito longo?
(c) Por que há um `SingleChildScrollView` envolvendo a `Column`? Que erro ele
previne?
(d) O `Divider` é um widget. Que outros widgets desta árvore existiriam como
"propriedade" em CSS ou XML, em vez de widget?

---

## Entrega

```
exercicios/aula03/
├── RESPOSTAS.md              # Partes A2, B, C (justificativa) e D
├── anatomia_app/
│   ├── lib/
│   │   ├── main.dart         # Parte A (comentado) + montagem da Parte C
│   │   └── widgets/
│   │       ├── cartao_aluno.dart
│   │       ├── cronometro.dart
│   │       └── contador_de_cliques.dart
│   └── pubspec.yaml
└── prints/
    ├── c1-cartao.png
    ├── c2-cronometro.png
    ├── c3-contador.png
    └── tela-completa.png
```

## Rubrica

| Critério | Pontos |
|---|---|
| Faz o que foi pedido | 40 |
| Qualidade do código (widgets separados, `const`, `final`, `analyze` limpo) | 25 |
| Interface e prints | 20 |
| Interpretação dos erros e da árvore | 15 |

---

## 🎁 Bônus (+10 pontos)

Torne o `CartaoAluno` **tocável** e **favoritável**:

- envolva o `Card` em um `InkWell` com `onTap:` que mostra uma `SnackBar`;
- adicione um ícone de estrela que alterna entre `Icons.star_border` e
  `Icons.star`;
- para isso, o `CartaoAluno` precisará virar `StatefulWidget`.

**E responda:** o favorito deveria mesmo morar **dentro** do cartão? Se a tela
precisasse saber quais alunos estão favoritados (para filtrar, por exemplo),
onde esse estado deveria ficar?

*(Essa pergunta é o assunto da Aula 22 — "lifting state up". Não precisa
resolver agora; só argumente.)*
