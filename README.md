
# これは何？

- vscode の source tree から Markdown Extension を切り出したものです
- 勉強用途やオレオレ Markdown プレビューなどを作成する場合に参考になります
- clone して、npm install を実行後、F5 キーを押せばすぐに動かすことができます(できるはず)

```bash
$ git clone https://github.com/satokaz/vscode-markdown-mock.git
$ cd vscode-markdown-mock
$ npm install
$ code .
```
- vscode に組み込まれている markdown extension を上書きします
- Preview の tab head には 🌸 のアイコンがつくので、少しわかりやすいです
- こちらの branch は、下記 2 つのコマンドを追加
  - 🌸 Markdown: 🌸 show HTML: Output Chnnel (出力) に HTML レンダリングを出力 
  - 🌸 Markdown: 🌸 create HTML File: ファイルに HTML を保存してブラウザで開く


# Changelog

- 2017/02/16: Sync with commit [7a82ac3ff9d46fefc5b24c86777a4d0d0656d936](https://github.com/Microsoft/vscode/commit/7a82ac3ff9d46fefc5b24c86777a4d0d0656d936)

# 切り出し方

- vscode を clone
- extension/markdown を適当な場所にコピー
- vscode で開く
- .gitignore の作成
- .vscodeignore の作成
- Git リポジトリの初期化


## package.json の編集
- package.json の aiKey を削除
- "scripts" を入れ替え、

```json
  "scripts": {
    "vscode:prepublish": "tsc -p ./",
    "compile": "tsc -watch -p ./",
    "postinstall": "node ./node_modules/vscode/bin/install"
  },
```

- 書き替え

```json
  "name": "vscode-markdown-mock",
  "displayName": "VS Code Markdown",
  "description": "Markdown for VS Code",
  "version": "0.2.0",
  "publisher": "Microsoft",
```


- "engines" を ^1.9.0 に書き替え (じゃないと新しい API が使えない) 

```json
  "engines": {
    "vscode": "^1.9.0"
  },
```

- src/typings/ref.d.ts を書き替える

```ts
/* /// <reference path='../../../../src/vs/vscode.d.ts'/> */
/// <reference path='../../node_modules/vscode/vscode.d.ts'/>
```


## npm install 
- npm install -SD vscode
- npm install


## Reload Windows

## extension.ts の編集

- telemetryReporter 関連をつぶす
- IPackageInfo 関連をつぶす 

## Debug: Open launch.json を実行

- .vscode/launch.json が作成される
- launch.json の中身を下記と入れ替える

```json
{
  "version": "0.2.0",
  "configurations": [

    {
      "type": "extensionHost",
      "request": "launch",
      "name": "拡張機能の起動",
      "runtimeExecutable": "${execPath}",
      "args": [
        "--extensionDevelopmentPath=${workspaceRoot}"
      ],
      "stopOnEntry": false,
      "sourceMaps": true,
      "outFiles": [
        "${workspaceRoot}/out"
      ],
      "preLaunchTask": "npm"
    }
  ]
}
```

## task runner の構成

- `Tasks: Configure Task Runner` を実行
- npm を選択して、中身を下記に入れ替える

```json 
// A task runner that calls a custom npm script that compiles the extension.
{
    "version": "0.1.0",

    // we want to run npm
    "command": "npm",

    // the command is a shell script
    "isShellCommand": true,

    // show the output window only if unrecognized errors occur.
    "showOutput": "silent",

    // we run the custom script "compile" as defined in package.json
    "args": ["run", "compile", "--loglevel", "silent"],

    // The tsc compiler is started in watching mode
    "isWatching": true,

    // use the standard tsc in watch mode problem matcher to find compile problems in the output.
    "problemMatcher": "$tsc-watch"
}
```

- F5 キーでデバッグ開始
- あとは、vsce をインストールして package が作れます
