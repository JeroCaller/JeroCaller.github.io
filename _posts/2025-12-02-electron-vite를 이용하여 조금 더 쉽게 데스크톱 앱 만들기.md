---
title: "[Electron] electron-vite를 이용하여 조금 더 쉽게 데스크톱 앱 만들기"
category: "Desktop"
tag: ["Desktop", "Desktop app", "Electron", "Web", "Node.js", "Framework", "Electron Vite", "Vite"]
---

이전 글인 “[**Electron - 데스크톱 앱 제작 프레임워크**](/desktop/Electron-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1-%EC%95%B1-%EC%A0%9C%EC%9E%91-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC/)”에서는 Electron이라는 프레임워크를 이용하여 HTML, CSS, JS와 같은 웹 기술만으로도 데스크톱 앱을 제작할 수 있음을 보았다. 하지만 이를 위해 초기에 설정해야할 것들이 은근 많아 번거롭다는 것이 문제였다. 그러나 electron-vite를 이용하면 초기 폴더 구조를 잡아주고, 초기에 필요한 파일들 및 설정들을 자동으로 생성, 적용해주기에 바로 앱 제작에 돌입할 수 있다는 장점이 있다. 어쩌면 Electron과 electron-vite의 관계는 초기 설정을 직접 개발자가 해줘야하는 Spring과 자동 구성이 있어 편리한 SpringBoot 간 관계에 비유할 수도 있겠다. 이 글에서는 electron-vite를 이용하여 electron 앱을 만드는 방법에 관해 살펴보도록 하겠다. 

# electron-vite 개요

[electron-vite](https://electron-vite.org/)는 기존의 Electron 프레임워크를 이용하여 좀 더 쉽고 빠르게 데스크톱 앱을 제작해줄 수 있는 build tool이다. 사실 기존의 웹 앱 제작을 위한 프론트엔드 build tool인 Vite가 Electron과 결합된 것이라 보면 되겠다. Vite에 대해서는 필자가 이전에 작성한 글인 “[**CRA 대신 리액트 프로젝트를 생성할 대체 수단 - Vite**](/react/CRA-%EB%8C%80%EC%8B%A0-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A5%BC-%EC%83%9D%EC%84%B1%ED%95%A0-%EB%8C%80%EC%B2%B4-%EC%88%98%EB%8B%A8-Vite/)”를 참고하면 되겠다. 

사실 이렇게 놓고 보면, 그냥 기존의 Vite를 이용하여 Electron 앱을 제작하면 될 것 같은데, 굳이 electron-vite라는 것이 왜 생겨났을까, 라는 의문이 떠오를 수도 있겠다. 기존 Vite를 이용한 Electron 앱 제작 또는 순수 Electron만으로 데스크톱 앱 제작 시 생겨나는 문제점들은 다음과 같다.

- 기존의 Vite를 이용하여 Electron 앱 제작 시 Vite만의 설정 파일인 `vite.config.js` 파일을 각각 `main` , `preload`, `renderer` 폴더 각각에 생성해야한다고 한다. 설정 파일이 따로따로 있어서 일원화된 관리가 어렵다고 한다.
- Vite를 사용하건 안하건 Electron 앱 제작을 위해 초기 파일(`main.js` 등) 생성 및 설정이 필요하다. 이는 매우 번거롭고 복잡한 일이다.
- 애초에 Vite는 “웹 앱” 제작을 위한 것이지 데스크톱 앱 제작이 목적은 아니다.

이로 인해 electron-vite라는 것이 생겨났다. electron-vite만의 장점은 다음과 같다. 

- Electron 앱 제작을 위한 초기 설정 및 폴더 구조가 자동으로 생성된다. `main` , `preload` , `renderer` 폴더 및 각각 필요한 파일들이 자동으로 생성된다. 따라서 개발자는 번거로운 초기 설정없이 바로 앱 제작에 돌입할 수 있게 된다.
- Vite 설정 파일을 하나로 합쳐 관리할 수 있다. `electron.vite.config.js` 파일이 프로젝트 루트에 처음 생성되는데, 여기서 각각 `main` , `preload`, `renderer` 에 대한 설정들을 별도로 줄 수 있다.
- `renderer` 에는 HMR(Hot Module Replacement)를 지원하여 개발 환경에서 일부 모듈의 소스 코드 변경 시 변경된 모듈만을 감지하여 자동으로 앱 화면에 반영해준다. `main` , `preload` 쪽에서는 Hot reloading을 지원하여 소스 코드 변경 시 자동으로 서버가 재시작되어 변경사항을 자동으로 반영해준다는 편리함이 있다. 그리고 이 과정도 빠르다. 즉, 개발 환경에서 소스 코드 변경 시 이를 자동으로 빠르게 앱 화면에 반영해준다.
- 자바스크립트로 만든 결과물의 경우 해커가 이를 unpacking하여 소스 코드를 읽어 악용할 수 있다고 한다. electron-vite에서는 이 소스 코드를 보호하는 기능을 제공한다. 이에 대한 자세한 사항은 “[**Source Code Protection**](https://electron-vite.org/guide/source-code-protection)” 참고.

electron-vite는 기존 Vite만의 장점과 더불어 더 좋은 개발자 경험과 보안을 제공한다는 점을 볼 수 있다. 이제 이 electron-vite를 이용하여 데스크톱 앱 제작을 위한 프로젝트 생성 및 여러 참고사항들, 그리고 패키징 방법에 대해서 살펴보도록 하겠다.

<style>
  details {
    background-color: #434555;
    margin-bottom: 1em;
  }
  details > summary {
    cursor: pointer;
    margin: 0.5em;
  }
  details a {
    text-decoration: none;
    margin: 0 0.5em;
  }
  details > div {
    padding: 0.5em;
  }
</style>
<details id="ref-1">
<summary>
    <strong>
        <i>참고) HMR VS Hot reloading</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
원래 자바스크립트를 이용한 개발 시 코드를 수정하게 되면 이 수정 사항을 반영시키기 위해 브라우저에서는 새로고침을, Node.js에서는 서버를 재시작해야만 했다. 이는 꽤 번거로운 작업일 것이다. 그래서 코드 수정을 자동으로 감지하여 자동으로 브라우저 새로고침 또는 서버 재시작을 하여 수정 사항을 반영하는 기술이 나타났는데 이를 Hot reloading이라 한다. 

Hot reloading의 경우 소스 코드 일부만 변경되어도 애플리케이션 전체가 재동작한다는 특징이 있다. 그래서 어떻게 보면 비효율적인 부분이 있다. 특히 프로젝트 규모가 클수록 재구동에 시간이 걸릴텐데, 이를 해결하기 위해 변경된 모듈에 대해서만 이를 감지하고 반영시켜주는 기술이 바로 Hot Module Replacement(HMR)이다. 

즉, 이 두 기술은 모두 “소스 코드 수정 시 자동으로 브라우저 재시작 또는 서버 재구동을 해준다”라는 점에선 공통점이 있으나, HMR은 이를 실행시켜주는 기술인 Hot reloading 기반 위에서 좀 더 효율적인 재시작을 해주는 기술이라 보면 되겠다. 
</div>
</details>


# electron-vite로 첫 프로젝트 생성하기

electron-vite를 이용하여 React + Typescript 기반 프로젝트를 생성해보도록 하겠다. 먼저 프로젝트를 생성하고자 하는 폴더 위치에서 다음의 명령어를 입력한다. 

```bash
npm create @quick-start/electron@latest
```

예제 1-1. electron-vite로 첫 프로젝트 생성 명령어.

그 후 다음 사진들의 내용을 따라 하면 된다.

⚠️ 아래 실습에서는 사실 `electron-study/vite` 폴더에 `first-electron-vite` 라는 프로젝트 폴더를 생성하려고 했으나 필자가 착각하여 미리 해당 폴더를 만들고 그 안에 생성하도록 하였다. 그래서 `electron-study/vite/first-electron-vite/first-electron-vite` 라는 이상한 경로가 생성되었다. 원래 의도대로 하려면 `electron-study/vite` 폴더 위치에서 아래 사진들과 같은 과정을 거치도록 해야 한다. electron-vite 프로젝트 생성 시 이를 착각하지 않도록 주의해야겠다. 

![사진 1-1.](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/1.png)

사진 1-1.

![사진 1-2.](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/2.png)

사진 1-2.

위 사진에서는 프로젝트명을 정해 입력한다. 

![사진 1-3.](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/3.png)

사진 1-3.

React, Vue 등의 여러 프레임워크 중 하나를 택한다. 

<style>
  .single-image {
    text-align: center;
    margin: 1em;
  }
  .single-image > p {
    margin-top: 0.5em;
  }
</style>
<div class="single-image">
  <img width="60%" src="/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/4.png" alt="image">
  <p>사진 1-4.</p>
</div>

여기서는 타입스크립트도 사용해볼 것이므로 추가하였다. 

<div class="single-image">
  <img width="60%" src="/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/5.png" alt="image">
  <p>사진 1-5. </p>
</div>

다음에 오는 모든 질문에 필자는 “Yes”로 결정하였다. 

![사진 1-6. ](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/6.png)

사진 1-6. 

위 사진이 뜨면 해당 폴더에 Electron-Vite 프로젝트가 생성된다. 해당 프로젝트 폴더의 구조는 다음과 같다. 

```
C:.
│   .editorconfig
│   .gitignore
│   .npmrc
│   .prettierignore
│   .prettierrc.yaml
│   dev-app-update.yml
│   electron-builder.yml
│   electron.vite.config.ts
│   eslint.config.mjs
│   package.json
│   README.md
│   tsconfig.json
│   tsconfig.node.json
│   tsconfig.web.json
│   
├───.vscode
│       extensions.json
│       launch.json
│       settings.json
│
├───build
│       entitlements.mac.plist
│       icon.icns
│       icon.ico
│       icon.png
│
├───resources
│       icon.png
│
└───src
    ├───main
    │       index.ts
    │
    ├───preload
    │       index.d.ts
    │       index.ts
    │
    └───renderer
        │   index.html
        │   
        └───src
            │   App.tsx
            │   env.d.ts
            │   main.tsx
            │
            ├───assets
            │       base.css
            │       electron.svg
            │       main.css
            │       wavy-lines.svg
            │
            └───components
                    Versions.tsx
```

예제 1-2. Electron-Vite 프로젝트 구조

이제 `package.json` 파일이 존재하므로 `npm i` 명령어를 통해 `node_modules` 모듈을 생성한 후, `npm run dev` 를 입력하면 앱이 실행되어 다음과 같은 창이 뜬다. 

![사진 1-7. 첫 electron-vite 앱 창](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/7.png)

사진 1-7. 첫 electron-vite 앱 창

## 프로젝트 폴더 구조 살펴보기

앞서 보았듯 프로젝트 폴더에는 src 폴더 아래에 각각 `main`, `preload`, `renderer` 폴더가 이미 존재하고, 각각의 폴더 안에 이미 작성되어 있는 코드들이 있기에 위와 같은 화면이 나올 수 있는 것이다. 

앞선 예제 1-2에서 보인 electron-vite의 프로젝트 폴더 내부를 보면 알겠지만, `src` 폴더 내부에 각각 `main`, `preload`, `renderer` 하위 폴더가 생성된 것을 볼 수 있다. 이는 Electron에서 사용하는 개념인 IPC(Inter-Process Communication)를 고려하여 각각 main process, preload, renderer process에서 사용될 모듈들을 분리하여 쉽게 관리하기 위해 고안된 구조라 보면 되겠다. IPC에 대해선 이전 글에서 설명한 적이 있으므로 “[Preload script와 IPC(Inter-Process Communication)](/desktop/Electron-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1-%EC%95%B1-%EC%A0%9C%EC%9E%91-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC/#preload-script%EC%99%80-ipcinter-process-communication)” 부분을 참고. 

원래 기존의 Vite만으로 Electron 앱을 만들기 위해 프로젝트 폴더를 구성할 때에는 `main`, `preload`, `renderer` 각각의 폴더 내부에 Vite 설정 파일인 `vite.config.ts` 가 각자 존재했다고 한다. 그러나 electron-vite를 사용하면 프로젝트 루트에 `electron.vite.config.ts` 파일 하나만 생성되기에 이 파일 하나로 각각 부문에 대한 설정들을 한꺼번에 할 수 있다는 장점이 있다. 실제로 해당 파일 내부를 보면 `main`, `preload` , `renderer` 각각의 설정들을 한꺼번에 할 수 있음을 볼 수 있다. 

```tsx
import { resolve } from 'path'
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()]
  },
  preload: {
    plugins: [externalizeDepsPlugin()]
  },
  renderer: {
    resolve: {
      alias: {
        '@renderer': resolve('src/renderer/src')
      }
    },
    plugins: [react()]
  }
})
```

예제 2-1. `electron.vite.config.ts` 

`src`  폴더 내부를 살펴보자면, 먼저 `renderer` 폴더 내에는 React 프로젝트의 폴더와 동일한 모습을 보인다. `App.tsx` 파일 내용은 다음과 같다. 

```tsx
import Versions from './components/Versions'
import electronLogo from './assets/electron.svg'

function App(): React.JSX.Element {
  const ipcHandle = (): void => window.electron.ipcRenderer.send('ping')

  return (
    <>
      <img alt="logo" className="logo" src={electronLogo} />
      <div className="creator">Powered by electron-vite</div>
      <div className="text">
        Build an Electron app with <span className="react">React</span>
        &nbsp;and <span className="ts">TypeScript</span>
      </div>
      <p className="tip">
        Please try pressing <code>F12</code> to open the devTool
      </p>
      <div className="actions">
        <div className="action">
          <a href="https://electron-vite.org/" target="_blank" rel="noreferrer">
            Documentation
          </a>
        </div>
        <div className="action">
          <a target="_blank" rel="noreferrer" onClick={ipcHandle}>
            Send IPC
          </a>
        </div>
      </div>
      <Versions></Versions>
    </>
  )
}

export default App
```

예제 2-2. `App.tsx`

`main` 폴더 내에는 다음과 같이 `index.ts` 파일 하나가 존재한다. 

```tsx
import { app, shell, BrowserWindow, ipcMain } from 'electron'
import { join } from 'path'
import { electronApp, optimizer, is } from '@electron-toolkit/utils'
import icon from '../../resources/icon.png?asset'

function createWindow(): void {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    width: 900,
    height: 670,
    show: false,
    autoHideMenuBar: true,
    ...(process.platform === 'linux' ? { icon } : {}),
    webPreferences: {
      preload: join(__dirname, '../preload/index.js'),
      sandbox: false
    }
  })

  mainWindow.on('ready-to-show', () => {
    mainWindow.show()
  })

  mainWindow.webContents.setWindowOpenHandler((details) => {
    shell.openExternal(details.url)
    return { action: 'deny' }
  })

  // HMR for renderer base on electron-vite cli.
  // Load the remote URL for development or the local html file for production.
  if (is.dev && process.env['ELECTRON_RENDERER_URL']) {
    mainWindow.loadURL(process.env['ELECTRON_RENDERER_URL'])
  } else {
    mainWindow.loadFile(join(__dirname, '../renderer/index.html'))
  }
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
  // Set app user model id for windows
  electronApp.setAppUserModelId('com.electron')

  // Default open or close DevTools by F12 in development
  // and ignore CommandOrControl + R in production.
  // see https://github.com/alex8088/electron-toolkit/tree/master/packages/utils
  app.on('browser-window-created', (_, window) => {
    optimizer.watchWindowShortcuts(window)
  })

  // IPC test
  ipcMain.on('ping', () => console.log('pong'))

  createWindow()

  app.on('activate', function () {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// Quit when all windows are closed, except on macOS. There, it's common
// for applications and their menu bar to stay active until the user quits
// explicitly with Cmd + Q.
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here.

```

예제 2-3. `src/main/index.ts` 

이전 글에서 소개한 Electron에서는 일일이 `main.js` 를 구성해야했었는데, electron-vite를 사용하면 그러한 번거로움없이 자동으로 구성되어 편리하다. 

한 편 `preload` 폴더에는 각각 `index.ts` 와 `index.d.ts` 파일이 존재한다. 

```tsx
import { contextBridge } from 'electron'
import { electronAPI } from '@electron-toolkit/preload'

// Custom APIs for renderer
const api = {}

// Use `contextBridge` APIs to expose Electron APIs to
// renderer only if context isolation is enabled, otherwise
// just add to the DOM global.
if (process.contextIsolated) {
  try {
    contextBridge.exposeInMainWorld('electron', electronAPI)
    contextBridge.exposeInMainWorld('api', api)
  } catch (error) {
    console.error(error)
  }
} else {
  // @ts-ignore (define in dts)
  window.electron = electronAPI
  // @ts-ignore (define in dts)
  window.api = api
}

```

예제 2-4. `src/preload/index.ts` 

```tsx
import { ElectronAPI } from '@electron-toolkit/preload'

declare global {
  interface Window {
    electron: ElectronAPI
    api: unknown
  }
}
```

예제 2-5. `src/preload/index.d.ts` 

`src/main` 쪽에서 기능을 구현하면 `preload` 쪽에서는 예제 2-4의 `api` 객체에 해당 함수를 추가하고, 예제 2-5인 `index.d.ts` 에 해당 함수의 타입을 추가하면 된다. 이에 대한 자세한 방법은 후술할 예정. 

이처럼 electron-vite를 이용하면 `main` , `preload` , `renderer` 각각으로 분리된 폴더 구조로 각 분야의 코드들을 쉽게 관리할 수 있고, `src/main/index.ts` , `src/preload/index.ts` 등 이미 앱 구동에 필요한 모듈들도 포함되어 있어서 기존의 Electron만 사용할 때의 번거로운 설정들을 하지 않아도 된다는 편리성이 있다. 또한 `vite.config.ts` 라는 Vite 설정 파일을 하나로 관리할 수 있다는 장점도 있다. 

# electron-vite 앱 구현 시 참고사항들

여기서부터 보일 코드들의 전체 소스 코드들은 [https://github.com/JeroCaller/electron-study/tree/main/vite/first-electron-vite-study](https://github.com/JeroCaller/electron-study/tree/main/vite/first-electron-vite-study) 여기를 참고.

## 로컬 파일 시스템 접근하여 이미지 파일을 앱에 불러오기

이번에는 앞서 생성한 프로젝트에서 사용자 로컬 디바이스에 있는 이미지를 불러와 앱으로 가져오는 기능을 구현해보도록 하겠다. 여기에는 로컬 파일 시스템 접근 시 처음에 설정해야할 사항들이 포함되어 있으니 참고.

electron-vite 앱에서 사용자가 “이미지 불러오기” 버튼을 클릭하면 다이얼로그 창이 뜨게 하고, 이를 통해 사용자가 자신의 로컬 디바이스에 있는 특정 이미지 파일을 선택하도록 한다. 그러면 해당 이미지 파일 경로를 읽어와 앱에 그 이미지를 보이도록 하는 기능을 구현하고자 한다. 그런데 여기서 주의해야할 사항이 있다. renderer 프로세스 쪽에서는 `<img>` 태그를 통해 해당 이미지를 앱으로 불러올려고 할 것인데, 이는 곧 브라우저에서(이전 글에서도 설명했지만, electron 앱에는 chromium이라는 브라우저를 내장한다. 즉, electron 앱은 엄연히 브라우저인 것이다) `file://` 프로토콜을 통해 로컬 디바이스에 접근하려는 시도이므로 이는 보안상 차단된다. 그래서 이미지가 앱 상에서 뜨지 않는 문제점이 발생할 것이다. 

이를 해결하기 위한 방법 중 하나로 커스텀 프로토콜을 만들어 `file://` 프로토콜을 대체하는 것이다. renderer 쪽에서는 이 커스텀 프로토콜로 이미지 요청을 하고, 이 요청을 받은 main process 쪽에서는 이 커스텀 프로토콜을 제거한 후 해당 이미지 파일을 불러오는 방식으로 구현하는 것이다. Electron에 대해 다뤘던 이전 글에서도 설명하였듯, main process에서는 사용자 로컬 파일 시스템에 접근하는 것이 가능하다는 점을 이용하는 것이다. 

먼저 `src/main` 폴더에 다음과 같은 `registerFileProtocol.ts` 파일을 생성하여 다음의 코드를 추가한다. 

```tsx
import { protocol, net } from 'electron';
import { pathToFileURL } from 'url';

const fileProtocolName = "app-asset";

/**
 * 사용자 로컬 디바이스 내 이미지 파일을 앱으로 불러오기 위한 커스텀 프로토콜 등록.
 * 
 * 기본적으로 사용자 로컬 디바이스 내 이미지 파일을 앱으로 불러올 때 "file://" 프로토콜로 불러오게 되는데, 
 * 이는 렌더러 프로세스, 브라우저에서 직접 로컬 파일 시스템으로 접근하려고 하기에 보안상 차단된다. 
 * 따라서 이를 해결하기 위해 다음의 절차를 거쳐야한다. 
 * 
 * 1. 커스텀 프로토콜명을 정한다. 예를 들어 "app-asset://"처럼.
 * 2. 렌더러 프로세스쪽에서 이미지 파일을 불러올 때 이미지 경로 앞에 해당 커스텀 프로토콜을 붙인다. 
 * 이는 메인 프로세스의 IpcMain.handle()에 들어갈 리스너 함수 내에서 미리 해당 작업을 처리해도 된다. 
 * 3. 메인 프로세스 측에서 `protocol.handle('app-asset', (request) => {...})` 함수를 호출하여 
 * 해당 커스텀 프로토콜로 요청이 들어올 때 이를 제거하고 해당 이미지 파일을 불러오도록 한다. 
 * 이는 렌더러 프로세스와 달리 메인 프로세스에서는 로컬 디바이스에 접근 가능하기에 가능한 일이다. 
 * 4. 렌더러 프로세스 측에 있는 index.html 파일의 `<meta>` 태그의 `content` 속성값 뒤에 있는 `img-src 'self' data:`
 * 이 뒤에 앞서 정한 커스텀 프로토콜명을 추가한다. 예) `img-src 'self' data: app-asset:`  주의할 점은 커스텀 프로토콜명
 * 뒤에 반드시 콜론(:)을 붙여야 한다.
 */
const registerFileProtocol = () => {
  protocol.handle(fileProtocolName, (request) => {
    const slicedUrl = request.url.slice(`${fileProtocolName}://`.length);

    // query parameter 삭제
    const pathOnly = slicedUrl.split('?')[0];

    // 'app-asset://' 부분을 제거하여 실제 파일 경로를 얻습니다.
    // URL 디코딩을 통해 공백 등 특수문자를 처리한다.
    const filePath = decodeURIComponent(pathOnly);

    try {
      const fileUrl = pathToFileURL(filePath);
 
      // 디코딩된 경로를 표준 file:// URL로 만들어 net.fetch에 전달합니다.
      // 이렇게 하면 Windows의 'C:\' 같은 경로도 안전하게 처리됩니다.
      return net.fetch(fileUrl.href);
    } catch (error) {
      // 에러 발생 시 빈 응답을 반환하여 앱 크래시를 방지
      return new Response(null, { status: 500, statusText: 'Internal Server Error' });
    }
  });
}

export default registerFileProtocol;
export { fileProtocolName };
```

예제 3-1. `src/main/registerFileProtocol.ts` 

여기서는 커스텀 프로토콜명을 `app-asset` 이라고 정하였다. 원하는대로 이름을 지어도 된다. 그 후, `process.handle()` 함수에 해당 커스텀 프로토콜을 어떻게 처리할지를 작성하면 된다. 위 코드에서는 해당 커스텀 프로토콜이 포함된 이미지 경로 내에서 해당 프로토콜을 제거하고 로컬 디바이스에서 해당 이미지를 불러오도록 하였다. 여기서 만든 커스텀 프로토콜은 사실 일종의 가상의 프로토콜이지 실제로 존재하는 프로토콜이 아니기에 해당 프로토콜을 제거하는 작업을 수행하는 것이다. 

그 다음, 사용자가 자신의 로컬 디바이스에서 이미지 파일을 선택할 수 있는 dialog 창을 띄우고, 선택된 이미지 파일 경로를 반환하는 기능을 만든다. 필자의 경우, `src/main` 폴더에 `/feat` 폴더를 만든 후, 그 안에 다음과 같이 `readImageFilePath.ts` 파일을 작성하였다. 

```tsx
import { dialog } from "electron"
import { fileProtocolName } from "../registerFileProtocol";
import { IPCMainHandlerInfo } from "../types";

const readImageFilePath = async (): Promise<string | null> => {
  const {canceled, filePaths} = await dialog.showOpenDialog(
    {
      properties: ['openFile'],
      filters: [
        {
          name: "image",
          extensions: ["png", "jpg", "jpeg"]
        },
      ],
    }
  );

  if (!canceled) {
    return `${fileProtocolName}://${encodeURIComponent(filePaths[0])}`;
  }

  return null;
}

const readImageFilePathInfo: IPCMainHandlerInfo = {
  channel: 'read-image-file-path',
  listener: readImageFilePath,
}

export default readImageFilePathInfo;

```

예제 3-2. `src/main/feat/readImageFilePath.ts` 

위 예제에 쓰인 `IPCMainHandlerInfo` 타입은 `src/main` 폴더에 `types.ts` 파일을 생성하여 다음과 같이 정의하였다. 

```tsx
export interface IPCMainHandlerInfo {
  channel: string;
  listener: (event: Electron.IpcMainInvokeEvent, ...args: any[]) => any;
}
```

예제 3-3. `src/main/types.ts`

위 예제 3-2에서는 다이얼로그 창을 띄워 사용자가 자신의 로컬 디바이스에서 이미지 파일만을 선택하도록 하고, 그 이미지 경로를 반환하도록 하고 있다. `dialog.showOpenDialog()` API는 Electron에서 제공하는 함수로, 보다 자세한 사용 방법은 “[여기](https://www.electronjs.org/docs/latest/api/dialog#dialogshowopendialogwindow-options)”를 참고하면 되겠다. 

위 예제 3-2에서는 읽어온 파일 경로의 맨 앞에 앞서 정의한 커스텀 프로토콜을 추가하여 반환하도록 구성하였다. 

이제 이 리스너 함수를 `ipcMain.handle()` 로 호출하면 되겠다. 필자는 main 쪽에서 이러한 방식으로 작성할 기능들이 앞으로 많아질 것을 대비하여 다음과 같이 구조를 설계하였다. 먼저 `src/main` 폴더에 `registerIPCHandlers.ts` 를 생성하여 다음의 코드를 작성하였다. 

```tsx
import { ipcMain } from "electron"
import readImageFilePathInfo from "./feat/readImageFilePath";
import { IPCMainHandlerInfo } from "./types";

const registerIPCHandlers = () => {
  const handlers: IPCMainHandlerInfo[] = [
    readImageFilePathInfo, // 여기에 추후 추가할 핸들러들을 추가하면 된다. 
  ];

  for (const handler of handlers) {
    ipcMain.handle(handler.channel, handler.listener);
  }
}

export default registerIPCHandlers;

```

예제 3-4. `src/main/registerIPCHandlers.ts` 

지금이야 구현한 기능이 “이미지 불러오기” 하나 뿐이지만, 실전에서는 수많은 기능들을 구현할 것이다. 그 기능들을 `src/main/index.ts` 에 직접 등록하는 방식으로 작성하면 해당 파일에는 다른 설정 코드들도 있어 가독성도 해칠뿐더러 관리하기가 쉽지 않을 것이다. 따라서 위와 같이 별도의 모듈로 관리하는 것이 더 편리할 것이라 판단하였다. 

위에서 작성한 함수를 `src/main/index.ts` 에서 호출하도록 하면 된다. 

```tsx
// 생략...

app.whenReady().then(() => {
  // Set app user model id for windows
  electronApp.setAppUserModelId('com.electron')

  // Default open or close DevTools by F12 in development
  // and ignore CommandOrControl + R in production.
  // see https://github.com/alex8088/electron-toolkit/tree/master/packages/utils
  app.on('browser-window-created', (_, window) => {
    optimizer.watchWindowShortcuts(window)
  })

  // IPC test
  ipcMain.on('ping', () => console.log('pong'))

  registerIPCHandlers();  // 추가
  registerFileProtocol();  // 추가

  createWindow()

  app.on('activate', function () {
    // On macOS it's common to re-create a window in the app when the
    // dock icon is clicked and there are no other windows open.
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})

// 생략...
```

예제 3-5. `src/main/index.ts` 

위 예제에서는 앞서 작성한 `registerIPCHandlers` 및 `registerFileProtocol` 함수도 호출하도록 하였다. 이들이 제 기능을 하도록 보장하기 위해 모두 `createWindow()` 이전에 호출하도록 하였다. 

이제 `preload` 에서 앞서 `ipcMain.handle` 로 등록한 리스너 함수를 renderer 쪽에서도 사용할 수 있도록 등록하는 과정이 필요하다. `src/preload/index.ts` 와 `src/preload/index.d.ts` 파일 각각에 다음과 같이 코드를 수정하였다. 

```tsx
import { contextBridge, ipcRenderer } from 'electron'
import { electronAPI } from '@electron-toolkit/preload'
import readImageFilePathInfo from '../main/feat/readImageFilePath'

// Custom APIs for renderer
const api = {
  readImageFilePath: () => ipcRenderer.invoke(readImageFilePathInfo.channel),  // 추가
}

// Use `contextBridge` APIs to expose Electron APIs to
// renderer only if context isolation is enabled, otherwise
// just add to the DOM global.
if (process.contextIsolated) {
  try {
    contextBridge.exposeInMainWorld('electron', electronAPI)
    contextBridge.exposeInMainWorld('api', api)
  } catch (error) {
    console.error(error)
  }
} else {
  // @ts-ignore (define in dts)
  window.electron = electronAPI
  // @ts-ignore (define in dts)
  window.api = api
}

```

예제 3-6. `src/preload/index.ts` 

```tsx
import { ElectronAPI } from '@electron-toolkit/preload'
import { readImageFilePathInfo } from '../main/feat/readImageFilePath';

// 추가 
export interface CustomAPI {
  readImageFilePath: typeof readImageFilePathInfo.listener;
}

declare global {
  interface Window {
    electron: ElectronAPI
    api: CustomAPI  // unknown -> CustomAPI로 변경
  }
}

```

예제 3-7. `src/preload/index.d.ts` 

위와 같이 설정하면 이제 renderer 쪽에서도 앞서 main 쪽에서 정의한 기능을 가져와 사용할 수 있게 된다. 

그 다음 `renderer` 쪽에서도 먼저 설정할 것이 있다. `index.html` 파일 내 `<meta>` 태그에 있는 `content` 속성값에는 이미 `content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: "` 이라고 대입되어 있을 것이다. 자세히 보면 맨 마지막에 `img-src` 부분이 있는데, 여기에 앞서 정의한 커스텀 프로토콜을 추가하면 된다. 

```html
<!doctype html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Electron</title>
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <!-- "data: app-asset:" 과 같이 추가.  -->
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: app-asset:"
    />
  </head>

  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

```

예제 3-8. `src/renderer/src/index.html` 

주의할 점은 커스텀 프로토콜명 뒤에 반드시 콜론(`:`)을 붙여야한다는 것이다. 이걸 붙이지 않으면 이미지를 불러올 수 없으니 주의. 또한 위와 같이 `index.html` 파일에 커스텀 프로토콜을 추가하지 않으면 여전히 이미지를 불러올 수 있으니 이 또한 잊지 말야할 점이다.  

이제 renderer 쪽에서 “이미지 불러오기” 버튼을 구현하겠다. 필자는 기존에 존재하던 `App.tsx` 파일을 다음과 같이 수정하여 구현하였다. 

```tsx
import Versions from './components/Versions'
import electronLogo from './assets/electron.svg'
import Button from './components/Button'
import { useState } from 'react'

function App(): React.JSX.Element {
  const [ imagePath, setImagePath ] = useState<string | null>(null);
  const ipcHandle = (): void => window.electron.ipcRenderer.send('ping')

  const handleReadImageFile = async () => {
    let imageFilePath = await window.api.readImageFilePath();

    console.log(`이미지 파일 경로: ${imageFilePath}`);
    setImagePath(imageFilePath);
  }

  return (
    <>
      <img alt="logo" className="logo" src={electronLogo} />
      <div className="creator">Powered by electron-vite</div>
      <div className="text">
        Build an Electron app with <span className="react">React</span>
        &nbsp;and <span className="ts">TypeScript</span>
      </div>
      <p className="tip">
        Please try pressing <code>F12</code> to open the devTool
      </p>
      <div className="actions">
        <Button text="Documentation" href="https://electron-vite.org/"></Button>
        <Button text="Send IPC (ping pong)" onClick={ipcHandle}></Button>
        <Button text="이미지 파일 불러오기" onClick={handleReadImageFile}></Button>
      </div>
      {imagePath ? <img src={imagePath} width="200px" /> : <></>}
      <Versions></Versions>
    </>
  )
}

export default App

```

예제 3-9. `src/renderer/src/App.tsx` 

위 예제에서 사용된 `<Button>` 컴포넌트는 `src/renderer/src/componets` 폴더에 `Button.tsx` 파일을 생성하여 구현하였다. 

```tsx
interface ButtonProps {
  text: string;
  href?: string;
  onClick?: () => void;
}

const Button = (buttonProps : ButtonProps) => {
  const {text, href, onClick} = buttonProps;

  return (
    <div className="action">
      <a 
        target="_blank" 
        rel="noreferrer" 
        href={href} 
        onClick={onClick}
      >
        {text}
      </a>
    </div>
  )
}

export default Button;

```

예제 3-10. `src/renderer/src/components/Button.tsx` 

이제 터미널에서 `npm run dev` 를 입력하여 앱을 실행해보면 다음과 같은 결과를 얻을 것이다.

![사진 2-1. “이미지 파일 불러오기” 버튼을 추가한 모습. 기존에 존재했던 `App.tsx` 코드를 재활용하였다. ](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/8.png)

사진 2-1. “이미지 파일 불러오기” 버튼을 추가한 모습. 기존에 존재했던 `App.tsx` 코드를 재활용하였다. 

<div class="single-image">
  <img width="60%" src="/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/9.png" alt="image">
  <p>사진 2-2. 앞선 “이미지 파일 불러오기” 버튼 클릭 시 뜨는 다이얼로그 창의 모습 일부분. 앞선 예제 3-2에 추가한 <code>filters</code>가 적용되어 해당 파일 확장자만 선택할 수 있도록 제한할 수 있다. </p>
</div>

![사진 2-3. 필자의 로컬 컴퓨터에서 특정 이미지를 불러온 결과](/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/10.png)

사진 2-3. 필자의 로컬 컴퓨터에서 특정 이미지를 불러온 결과

## import 경로 줄이기

```tsx
import icon from '../../resources/icon.png?asset'
```

예제 4-1. 

앱을 제작하다보면 여러 폴더들이 더 깊게 생성될 수 있고, 이러면  바깥 폴더에 있는 다른 모듈을 import 할 때 위 코드처럼 상대 경로로 가져올 때 `../` 와 같은 상대 경로가 꽤 길어질 것이다. 이는 가독성에도 좋지 않을 뿐더러 혹시라도 import하는 모듈의 위치가 바뀌면 import 상대 경로도 바꿔줘야 한다는 문제점이 발생한다. electron-vite를 사용한다면 이 문제를 경로 별칭(path alias)을 부여하여 해결할 수 있다. 

path alias를 부여하고 사용하기 위해선 다음의 파일들에 추가 작업을 진행해야 한다.

- `electron.vite.config.ts`
- `tsconfig.node.json`
- `tsconfig.web.json`

먼저 `electron.vite.config.ts` 에서는 `main` , `preload` , `renderer` 각각에 대해 다음과 같이 path alias를 추가할 수 있다. 

```tsx
import { resolve } from 'path'
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()],
    resolve: {
      alias: {
        '@main': resolve('src/main'),
      }
    }
  },
  preload: {
    plugins: [externalizeDepsPlugin()],
    resolve: {
      alias: {
        '@main': resolve('src/main'),  // preload에서 main 쪽을 path alias로 import 하기 위함.
        '@preload': resolve('src/preload'),
      }
    }
  },
  renderer: {
    resolve: {
      // 보안상 renderer process는 직접 main process에 접근할 수 없으므로, 
      // 프로세스 분리 및 보안을 위해서라도 main 폴더에 대한 별칭은 주지 않는 것이 좋겠다. 
      alias: {
        '@renderer': resolve('src/renderer/src'),
      }
    },
    plugins: [react()]
  }
})

```

예제 4-2. `electron.vite.config.ts` 

위 예제에서 각각의 `main` , `preload`, `renderer` 설정값 내에 `resolve.alias` 를 추가하면 된다. `alias` 객체 내부에 들어가는 속성의 key에는 실제 모듈을 import할 때 사용할 별칭을, value에는 `resolve(...)` 를 이용하여 실제 경로를 입력하면 된다. 그러면 코드 작성 시 `resolve()` 에 명시한 경로 대신 별칭을 사용하여 import 할 수 있게 된다. 

여기서 끝이 아니다. 타입스크립트를 사용하는 경우에는 다음과 같이 `tsconfig.node.json` , `tsconfig.web.json` 파일에도 해당 별칭 정보를 추가해줘야 한다. 

```tsx
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.node.json",
  "include": ["electron.vite.config.*", "src/main/**/*", "src/preload/**/*"],
  "compilerOptions": {
    "composite": true,
    "moduleResolution": "node",
    "types": ["electron-vite/node"],
    // 아래 코드 추가
    "baseUrl": ".",  // paths의 기준 경로 설정. 
    "paths": {
      "@main/*": ["src/main/*"],
      "@preload/*": ["src/preload/*"],
    }
  }
}
```

예제 4-3. `tsconfig.node.json` 

```tsx
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.web.json",
  "include": [
    "src/renderer/src/env.d.ts",
    "src/renderer/src/**/*",
    "src/renderer/src/**/*.tsx",
    "src/preload/*.d.ts"
  ],
  "compilerOptions": {
    "composite": true,
    "moduleResolution": "node",
    "jsx": "react-jsx",
    // 아래 코드 추가
    "baseUrl": ".",
    "paths": {
      "@renderer/*": ["src/renderer/src/*"],
    }
  }
}

```

예제 4-4. `tsconfig.web.json`

Electron에서의 IPC에 관한 내용에서도 보았겠지만, renderer process와 main process는 IPC 없이는 직접 상호작용을 하는 것이 불가능하다. 또한 renderer에서 직접 main에 접근할 수 있게 되면 보안적으로도 별로 좋지 않은 행위이다(앞서 로컬 이미지 가져오기 예제와 똑같은 원리라 보면 된다. 만약 브라우저에서 사용자 로컬 기기에 직접 접근 가능하다면? 해커가 이를 악용할지도 모른다). 이와 같은 이유로 프로세스 분리 및 보안을 위해서라도 renderer에서 직접 main에 있는 모듈을 import 하도록 하는건 좋지 않을 것이라 판단하였다. 따라서 renderer에는 `@main` 별칭을 부여하지 않았다. 실제 코드를 작성할 때에도 왠만하면 renderer에서 main 폴더의 모듈을 직접 import 하지 않도록 하는 것이 좋겠다. 

이제 경로 별칭에 대한 설정은 끝났다. 앞서 작성한 코드들의 import 구문을 앞서 설정한 별칭으로 바꿔보자. 

```tsx
//import readImageFilePathInfo from '../main/feat/readImageFilePath'
import readImageFilePathInfo from '@main/feat/readImageFilePath';
```

예제 4-5. `src/preload/index.ts` 

그러면 상대 경로 없이도 깔끔하게, 그리고 파일 위치가 바뀌어도 문제 없이 코드를 작성할 수 있게 된다. 

## 앱 작동에는 문제가 없으나 코드에 빨간 줄이 생길 때 대처법

필자는 앞서 설명한 대로 electron-vite 프로젝트를 생성하였을 때, 앱 작동에는 문제가 없었으나 IDE에서는 `App.tsx` 와 같은 곳에 코드에 빨간 줄이 처지는 현상을 발견하였다. 사실 이는 필자가 이전에 쓴 글인 [typescript-리액트에-타입스크립트-적용해보기-with-Vite/#주의사항](/typescript/typescript-%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%97%90-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0-with-Vite/#%EC%A3%BC%EC%9D%98%EC%82%AC%ED%95%AD) 에서도 언급된 사항과 동일하다. 

electron-vite에서는 typescript 사용 시 `tsconfig.node.json` 과 `tsconfig.web.json` 이 두 파일이 프로젝트 루트에 존재할 것인데, 이 각각의 파일에 `compilerOptions.moduleResolution` 속성을 추가하고 이 속성에 `'node'` 라는 값을 주면 된다. 

```tsx
{
  "extends": "@electron-toolkit/tsconfig/tsconfig.web.json",
  "include": [
    "src/renderer/src/env.d.ts",
    "src/renderer/src/**/*",
    "src/renderer/src/**/*.tsx",
    "src/preload/*.d.ts"
  ],
  "compilerOptions": {
    "composite": true,
    "moduleResolution": "node",  // <- 추가
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": {
      "@renderer/*": ["src/renderer/src/*"],
      "@src/*": ["src/*"]
    }
  }
}

```

예제 5-1. `tsconfig.web.json`

위와 같이 설정하면 더 이상 IDE에서 코드에 빨간 줄이 뜨지 않을 것이다. 

# electron-vite 앱 빌드 및 패키징

코드 작성을 완료하였다면 이제 이를 실행 가능한 앱으로 변환하여 배포할 준비를 해야할 것이다. electron-vite에서는 다음과 같은 과정을 거쳐 최종적으로 배포 가능한 앱을 생성한다. 

1. React, Typesript 등의 웹 기술로 제작한 소스 코드를 브라우저 및 Node.js가 이해할 수 있는 순수 Javascript, HTML, CSS 등으로 컴파일 후 번들링한다. electron-vite에서는 이 결과물이 `out` 폴더에 담긴다. 이 단계에서는 아직 실행 가능한 앱이 생성된 것은 아니고, 컴파일되어 번들링된 소스 코드 묶음만이 존재한다. 👉 빌드 단계
2. Electron Forge나 Electron Builder와 같은 도구를 이용하여 `out` 폴더에 생성된 번들링 결과물을 패키징하여 결과적으로 배포 가능한 앱을 생성한다.  👉 패키징 단계

먼저, 직접 첫 번째 단계인 빌드를 해보자면 다음과 같다. `package.json` 의 `"scripts"` 속성을 보면 `"build": "npm run typecheck && electron-vite build"` 가 있는 것을 볼 수 있다. 이를 이용하여, 터미널 창에 `npm run build` 를 실행하면 된다. 그러면 `out` 폴더에 빌드 결과가 잠시 후에 생성될 것이다. 

```
out/
├───main
│       index.js
│
├───preload
│       index.js
│
└───renderer
    │   index.html
    │
    └───assets
            electron-DtwWEc_u.svg
            index-B4axxrkd.css
            index-C1lUyAk-.js
```

예제 6-1. `out` 폴더 구조

`src` 폴더 내 구조와 동일한 구조인 것을 확인할 수 있다. 

한 가지 주의할 점은, 빌드 결과물을 `electron.vite.config.ts` 파일 내 `build.outDir` 속성을 통해 각각 `main` , `preload` , `renderer` 폴더의 빌드 결과물을 놓을 위치를 별도로 지정할 수도 있는데, 이 세 폴더는 모두 앱 실행에 필수적인 요소들이므로 위에서 보인 `out` 폴더처럼 모두 한 폴더 안에 결과물이 저장될 수 있도록 해야한다는 것이다. 

두 번째 단계인 패키징을 위해선 패키징을 위한 도구가 필요한데, 크게 Electron Builder와 Electron Forge 이 둘이 알려져 있다. 후자의 경우 이전에 Electron에 관한 글을 다뤘을 때 다룬 적이 있었다. 만약 Electron Forge를 이용하여 electron-vite 앱 패키징을 하고자 하는 경우, 이전 글의 “[앱 패키징](/desktop/Electron-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1-%EC%95%B1-%EC%A0%9C%EC%9E%91-%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC/#%EC%95%B1-%ED%8C%A8%ED%82%A4%EC%A7%95)”과 electron-vite 공식 문서에서 소개하는 “[**Distributing Apps With Electron Forge**](https://electron-vite.org/guide/distribution#distributing-apps-with-electron-forge)” 글을 참고하면 되겠다. 여기서는 Electron Builder를 사용해보도록 하겠다. 

사실 electron-vite로 프로젝트를 생성할 떄 이미 프로젝트 루트에는 `electron-builder.yml` 파일이 다음과 같이 자동으로 생성되어 있다. 

```yaml
appId: com.electron.app
productName: first-electron-vite-study
directories:
  buildResources: build
files:
  - '!**/.vscode/*'
  - '!src/*'
  - '!electron.vite.config.{js,ts,mjs,cjs}'
  - '!{.eslintcache,eslint.config.mjs,.prettierignore,.prettierrc.yaml,dev-app-update.yml,CHANGELOG.md,README.md}'
  - '!{.env,.env.*,.npmrc,pnpm-lock.yaml}'
  - '!{tsconfig.json,tsconfig.node.json,tsconfig.web.json}'
asarUnpack:
  - resources/**
win:
  executableName: first-electron-vite-study
nsis:
  artifactName: ${name}-${version}-setup.${ext}
  shortcutName: ${productName}
  uninstallDisplayName: ${productName}
  createDesktopShortcut: always
mac:
  entitlementsInherit: build/entitlements.mac.plist
  extendInfo:
    - NSCameraUsageDescription: Application requests access to the device's camera.
    - NSMicrophoneUsageDescription: Application requests access to the device's microphone.
    - NSDocumentsFolderUsageDescription: Application requests access to the user's Documents folder.
    - NSDownloadsFolderUsageDescription: Application requests access to the user's Downloads folder.
  notarize: false
dmg:
  artifactName: ${name}-${version}.${ext}
linux:
  target:
    - AppImage
    - snap
    - deb
  maintainer: electronjs.org
  category: Utility
appImage:
  artifactName: ${name}-${version}.${ext}
npmRebuild: false
publish:
  provider: generic
  url: https://example.com/auto-updates
electronDownload:
  mirror: https://npmmirror.com/mirrors/electron/

```

예제 6-2. `electron-builder.yml`

그리고 `package.json` 파일의 `"scripts"` 속성에는 다음과 같은 속성들이 자동으로 생성되어 있다. 

```json
"build:win": "npm run build && electron-builder --win",
"build:mac": "electron-vite build && electron-builder --mac",
"build:linux": "electron-vite build && electron-builder --linux"
```

예제 6-3. `package.json` 

따라서, 패키징 전 설정하고자 하는 바가 있다면 `electron-builder.yml` 파일 내용을 수정하면 될 것이고, 예를 들어 windows OS 기반 앱으로 패키징하고자 한다면 `npm run build:win` 이라 입력하면 된다. 

`npm run build:win` 명령어를 입력하고 잠시 기다리면 프로젝트 루트에 `dist` 라는 폴더가 생성되고 그 안에 여러 파일들이 생성되는데, 그 중 `{앱 이름}-{버전}-setup.exe` 파일도 생성된다. 파일 탐색기에서 해당 파일을 찾아 실행해보면 바탕화면에 해당 앱의 바로 가기 아이콘이 생성되며, 이를 실행시켜보면 앞서 만든 앱이 실행된다. 

<div class="single-image">
  <img width="60%" src="/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/11.png" alt="image">
  <p>사진 3-1. 패키징 후 dist 폴더 내 모습. <code>~setup.exe</code> 파일을 실행하면 잠시 뒤 electron 앱이 바탕화면에 “바로 가기 아이콘”으로 설치된다. </p>
</div>

<div class="single-image">
  <img width="20%" src="/images/2025-12-02/electron-vite%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20%EC%A1%B0%EA%B8%88%20%EB%8D%94%20%EC%89%BD%EA%B2%8C%20%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%86%B1%20%EC%95%B1%20%EB%A7%8C%EB%93%A4%EA%B8%B0/12.png" alt="image">
  <p>사진 3-2. 앞선 <code>~setup.exe</code> 파일 실행 후 바탕화면에 생성된 electron 앱  </p>
</div>

사실 앱 배포를 위해서는 앞선 빌드 과정을 개발자가 직접 수행할 필요는 없다. 이미 Electron Builder를 이용하여 패키징하는 과정에서 내부적으로 `out` 폴더에 번들링된 결과물을 생성한 후, 그 결과물을 토대로 패키징하기 때문이다. 

---

References

[1] Electron-Vite 공식 홈페이지

[electron-vite \| Next Generation Electron Build Tooling](https://electron-vite.org/)

[2] 내 블로그 - Vite

[CRA 대신 리액트 프로젝트를 생성할 대체 수단 - Vite](/react/CRA-%EB%8C%80%EC%8B%A0-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A5%BC-%EC%83%9D%EC%84%B1%ED%95%A0-%EB%8C%80%EC%B2%B4-%EC%88%98%EB%8B%A8-Vite/)

[3] `@vitejs/plugin-react` 버전별 changelog

[https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md)

[4] Path alias

[타입스크립트의 경로 맵핑 (baseUrl, paths)](https://www.daleseo.com/tsconfig-path-mapping/)

[5] Hot reload & Hot Module Replacement(HMR)

[HMR 이해하기](https://gseok.github.io/tech-talk-2022/2022-01-24-what-is-HMR/)