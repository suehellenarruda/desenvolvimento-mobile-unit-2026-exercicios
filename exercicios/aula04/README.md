# Exercício — Aula 04: Android Debug Bridge (adb)

**Disciplina:** Desenvolvimento Mobile (`F105880`) — UNIT 2026
**Entrega:** até a véspera da Aula 05 — PR `[Aula 04] Seu Nome Completo`

> Esta lista é quase toda de **terminal**. Cole a saída **real** dos comandos —
> saída inventada é fácil de reconhecer e zera o item.

---

## Parte A — Ficha técnica do aparelho (20 pontos)

**A1 (12 pts).** Cole a saída de cada comando e preencha a ficha:

```bash
adb version
adb devices -l
adb shell getprop ro.product.model
adb shell getprop ro.product.manufacturer
adb shell getprop ro.build.version.release
adb shell getprop ro.build.version.sdk
adb shell wm size
adb shell wm density
```

| Item | Valor |
|---|---|
| Modelo | |
| Fabricante | |
| Android | |
| Nível de API | |
| Resolução (px) | |
| Densidade (dpi) | |

**A2 (8 pts).** Calcule a **largura em dp** do seu aparelho:

```
largura_dp = largura_px / (dpi / 160)
```

Mostre a conta. Depois responda:

(a) Por que a referência é **160** dpi?
(b) Pergunte a **dois colegas** a largura em dp dos aparelhos deles. Os números
ficaram próximos, mesmo com resoluções diferentes? O que isso demonstra sobre
para que serve o dp?
(c) Se você fixasse um `Container(width: 400)` no Flutter, ele caberia na tela
do seu aparelho? E na do colega com a menor largura em dp?

---

## Parte B — Ciclo completo de vida do app (25 pontos)

Use o projeto `ola_unit` (Aula 02) ou `anatomia_app` (Aula 03).

Execute a sequência e cole a saída de **cada** passo:

```bash
flutter build apk --debug
ls -lh build/app/outputs/flutter-apk/
adb install -r build/app/outputs/flutter-apk/app-debug.apk
adb shell pm list packages | grep unit
adb shell monkey -p br.edu.unit.<seu_app> 1
adb exec-out screencap -p > prints/app-instalado.png
adb shell pm clear br.edu.unit.<seu_app>
adb uninstall br.edu.unit.<seu_app>
```

**B1 (12 pts).** A sequência completa, com as saídas.

**B2 (8 pts).** Compare os tamanhos:

```bash
flutter build apk --debug
flutter build apk --release
ls -lh build/app/outputs/flutter-apk/
```

| Build | Tamanho |
|---|---|
| `app-debug.apk` | |
| `app-release.apk` | |

**Explique a diferença.** O que o build de debug carrega que o de release não
carrega? (Releia o slide de modos de compilação da Aula 01.)

**B3 (5 pts).** O experimento do `exec-out`:

```bash
adb exec-out screencap -p > correto.png
adb shell    screencap -p > corrompido.png
ls -lh correto.png corrompido.png
```

Tente abrir os dois. Qual funciona? Cole os tamanhos e **explique a causa** da
corrupção.

---

## Parte C — Diagnóstico por logcat (30 pontos)

A parte mais importante da lista. Você vai **plantar** três erros diferentes e
**encontrar cada um pelo log**.

Para cada caso: deixe `adb logcat -c && adb logcat *:E` rodando em um terminal e
`flutter run` em outro.

### C1 (10 pts) — Exceção em callback de botão

```dart
void _incrementCounter() {
  if (_counter == 3) {
    throw Exception('Erro proposital C1');
  }
  setState(() => _counter++);
}
```

Registre: a linha do log com a exceção, o **arquivo e a linha** apontados, e se
o app **fechou** ou continuou.

### C2 (10 pts) — Erro antes do Flutter subir

```dart
Future<void> main() async {
  throw Exception('Erro proposital C2 - antes do runApp');
}
```

Registre a saída do `adb logcat *:E` **e** a do `flutter logs`.

**A pergunta que importa:** qual das duas ferramentas mostrou o erro? Por quê?

### C3 (10 pts) — Erro de permissão do sistema

Adicione ao `pubspec.yaml`: `flutter pub add http`. No app, faça uma requisição:

```dart
import 'package:http/http.dart' as http;
// dentro de um botão:
final r = await http.get(Uri.parse('https://example.com'));
debugPrint('status: ${r.statusCode}');
```

**Sem** declarar a permissão no `AndroidManifest.xml`, rode e toque no botão.

Registre o erro do logcat. Depois **corrija**, adicionando dentro de
`<manifest>` em `android/app/src/main/AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Rode de novo e mostre que funcionou.

**Responda:** por que este erro **não** é um erro de Dart? Quem o gerou?

---

## Parte D — adb por Wi-Fi (15 pontos)

**D1 (10 pts).** Conecte o aparelho **sem cabo** e documente o processo
completo, com as saídas:

```bash
# Android 11+
adb pair <ip>:<porta>
adb connect <ip>:<porta>

# Android 10 ou anterior (precisa do cabo uma vez)
adb tcpip 5555
adb shell ip route | awk '{print $9}'
adb connect <ip>:5555

adb devices -l          # deve mostrar o IP em vez do serial
```

Depois, **desconecte o cabo** e rode `flutter run`. Cole o print do app
rodando sem cabo.

**D2 (5 pts).** Responda:

(a) Funcionou no Wi-Fi da faculdade? Se não, qual o erro e qual a causa provável?
(b) Que risco de segurança existe com o adb por Wi-Fi ligado? Como desligar?
(c) Por que isso vai ser **necessário** na Aula 38?

---

## Parte E — Bônus: automação (+10 pontos)

Escreva um script `deploy.sh` (ou `deploy.ps1`) que faça, em um comando:

1. verifica se há aparelho conectado (aborta com mensagem clara se não houver);
2. `flutter build apk --debug`;
3. desinstala a versão anterior (sem falhar se não existir);
4. instala a nova;
5. abre o app;
6. captura um print em `prints/deploy-<timestamp>.png`;
7. abre o `logcat` filtrado no seu app.

Requisitos: `set -e`, mensagens legíveis a cada etapa, e o `application ID`
numa variável no topo.

Cole o script **e** a saída de uma execução bem-sucedida.

---

## Entrega

```
exercicios/aula04/
├── RESPOSTAS.md        # Partes A-D e E
├── deploy.sh           # Parte E (bônus)
└── prints/
    ├── app-instalado.png
    ├── correto.png / corrompido.png
    ├── adb-devices-wifi.png
    └── app-sem-cabo.png
```

## Rubrica

| Critério | Pontos |
|---|---|
| Faz o que foi pedido | 40 |
| Diagnóstico e interpretação dos logs | 25 |
| Evidências (saídas reais, prints) | 20 |
| Cuidado com detalhes | 15 |
| **Bônus script** | +10 |

---

## 🆘 `adb: command not found`

O `adb` está em `<android-sdk>/platform-tools`. Adicione ao `PATH`:

```bash
# macOS
export PATH="$HOME/Library/Android/sdk/platform-tools:$PATH"
# Linux
export PATH="$HOME/Android/Sdk/platform-tools:$PATH"
# Windows (PowerShell, permanente)
$a=[Environment]::GetEnvironmentVariable("Path","User")
[Environment]::SetEnvironmentVariable("Path","$a;$env:LOCALAPPDATA\Android\Sdk\platform-tools","User")
```

Coloque a linha do `export` no `~/.zshrc` ou `~/.bashrc` para não perder ao
fechar o terminal.
