
- extension/markdown を適当な場所にコピー
- vscode で開く
- .gitignore の作成
- .vscodeignore の作成
- Git リポジトリの初期化

## extension.js の編集

- telemetryReporter 関連をつぶす
- IPackageInfo 関連をつぶす 

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
  "name": "vscode-markdown",
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


## Debug: Open launch.json を実行

- .vscode/launch.json が作成される
- 構成の追加から、「{} VS Code 拡張機能の開発」を選択する。下記が追加される。

```json
    {
      "type": "extensionHost",
      "request": "launch",
      "name": "拡張機能の起動",
      "runtimeExecutable": "${execPath}",
      "args": [
        "--extensionDevelopmentPath=${workspaceRoot}"
      ],
      "sourceMaps": true,
      "outFiles": [
        "${workspaceRoot}/out/**/*.js"
      ],
      "preLaunchTask": "npm"
    },
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

- デバッグビューに移動するか
