# React + Typescript + Electron

> [https://cpro95.tistory.com/185](https://cpro95.tistory.com/185)

## create react app

```bash
yarn create react-app my-app --template typescript
```

## add electron

```bash
yarn add --dev electron electron-builder concurrently wait-on cross-env
yarn add electron-is-dev
```

- electron : Electron 기본 패키지입니다.
- electron-builder : electron을 실행파일 형태로 빌드해줍니다.
- concurrently : 두 개 이상의 명령어를 실행할 수 있게 해 줍니다.
- wait-on : 특정 파일(포트)이 활성화될 때까지 대기해 줍니다.
- cross-env : 윈도즈 환경이든 맥, 리눅스 환경이든 시스템에 관계없이 환경변수 값을 설정할 수 있게 해 줍니다.

## electron.js

- create `electron.js` in public folder

```js
const { app, BrowserWindow } = require("electron");
const path = require("path");
const isDev = require("electron-is-dev");

let mainWindow;

function createWindow() {
  mainWindow = new BrowserWindow({
    width: 900,
    height: 680,
    webPreferences: {
      nodeIntegration: true,
      enableRemoteModule: true,
      devTools: isDev,
    },
  });

  mainWindow.loadURL(
    isDev
      ? "http://localhost:3000"
      : `file://${path.join(__dirname, "../build/index.html")}`
  );

  if (isDev) {
    mainWindow.webContents.openDevTools({ mode: "detach" });
  }

  mainWindow.setResizable(true);
  mainWindow.on("closed", () => (mainWindow = null));
  mainWindow.focus();
}

app.on("ready", createWindow);

app.on("window-all-closed", () => {
  if (process.platform !== "darwin") {
    app.quit();
  }
});

app.on("activate", () => {
  if (mainWindow === null) {
    createWindow();
  }
});
```

## package.json

```json
  "main": "public/electron.js",
  "homepage": "./",
  "build": {
    "productName": "productName",
    "asar": true,
    "appId": "com.app.id"
  },

  "scripts": {
    "react-start": "react-scripts start",
    "react-build": "react-scripts build",
    "react-test": "react-scripts test",
    "react-eject": "react-scripts eject",
    "start": "concurrently \"cross-env NODE_ENV=development BROWSER=none yarn react-start\" \"wait-on http://localhost:3000 && electron .\"",
    "build": "yarn react-build && electron-builder",
    "release": "yarn react-build && electron-builder --publish=always"
  },
```
