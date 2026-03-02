---
title: "[electron-vite][Trouble Shooting] “'vitejs/plugin-react' 모듈 또는 해당 형식 선언을 찾을 수 없습니다.”"
category: "Desktop"
tag: ["Desktop", "Desktop app", "Electron", "Web", "Node.js", "Framework", "Electron Vite", "Vite", "TroubleShooting"]
---


# 배경

사용 기술

- electron-vite
- Typescript
- React

# 문제

![사진 1-1. 문제의 사진](/images/2025-12-03/electron-vite-Trouble%20Shooting-'vitejs-plugin-react'%20%EB%AA%A8%EB%93%88%20%EB%98%90%EB%8A%94%20%ED%95%B4%EB%8B%B9%20%ED%98%95%EC%8B%9D%20%EC%84%A0%EC%96%B8%EC%9D%84%20%EC%B0%BE%EC%9D%84%20%EC%88%98%20%EC%97%86%EC%8A%B5%EB%8B%88%EB%8B%A4/1.png)

사진 1-1. 문제의 사진

`electron.vite.config.ts` 파일 내 `@vitejs/plugin-react` 모듈 또는 그 타입 선언을 찾을 수 없다는 에러가 떴다. `npm run dev` 를 통해 개발 환경에서 앱을 구동시킬 때에는 아무런 문제가 없지만, 이를 빌드하려고 할 때 문제가 발생하기에 그냥 지나칠 수는 없었던 문제였다.

이 상태에서 터미널 창에서 `npm run build` 를 입력하면 다음의 메시지가 뜬다. 

```bash
> npm run build

> first-electron-vite-study@1.0.0 build
> npm run typecheck && electron-vite build

> first-electron-vite-study@1.0.0 typecheck
  There are types at 'C:/electron-study/vite/first-electron-vite-study/node_modules/@vitejs/plugin-react/dist/index.d.ts', but this result could not be resolved under your current 'moduleResolution' setting. Consider updating to 'node16', 'nodenext', or 'bundler'.

3 import react from '@vitejs/plugin-react'
                    ~~~~~~~~~~~~~~~~~~~~~~

Found 1 error in electron.vite.config.ts:3
```

# 해결 시도 1 - ❌

이전의 `npm run build` 이후 나온 에러 메시지대로 `tsconfig.*.json` 파일 내 `"moduleResolution"` 의 값을 각각 `"node16"`, `"nodenext"` , `"bundler"` 로 바꿔가며 시도를 해보았다. 그러나 이 경우 renderer 쪽의 `App.tsx` 내 코드에 타입 에러가 발생하거나 main, renderer 쪽 모두 import하는 모듈을 가져올 수 없다는 등의 다른 에러들이 발생하였다. 어떤 에러에서는 `package.json` 에 `"type": "module"` 을 넣으라고 해서 넣었는데도 여전히 에러는 그대로 발생하였다. 그래서 어쩔 수 없이 처음으로 되돌아갈 수밖에 없었다. 

# 해결 시도 2 - ✅

`@vitejs/plugin-react` Github를 방문해보니 다음과 같은 문서가 있었다. 

[https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md)

해당 모듈의 각 버전별 changelog였다. 4.7.0이 4 버전대의 마지막이었고, 그 이후부터는 5 버전대로 넘어가고 있었다. 혹시 몰라 `package.json` 의 `"devDependencies"` 속성 내부에 있는 `"@vitejs/plugin-react"` 속성값을 기존 `"^5.0.3"` 에서 `"^4.7.0"` 로 바꾼 후 다시 `npm i` 를 통해 `node_modules` 를 재설치하였고, 혹시 몰라 VSCode IDE에서 `ctrl + shift + p` 를 눌러 `"개발자: 창 다시 로드"` 를 클릭하여 IDE를 재실행하였다. 그랬더니 해당 모듈에 대한 에러도 사라졌고, `npm run build` 를 통한 빌드도 문제없이 진행되었다. 

아무래도 해당 모듈의 버전대 간 어떠한 차이 때문에 발생한 문제로 보이는데, changelog만 봤을 때는 아직 해당 에러에 대한 4 버전대와 5 버전대의 차이점이 있는지 파악하지 못한 상태이다. 그래서 현재는 사실상 반쪽짜리 해결인 셈이다. 추후에 해당 문제까지 해결한다면 이 글에 추가하도록 하겠다. 

# 해결 시도 3 - ✅

<p class="notice--info">2026-03-02 새로운 내용 추가</p>

VSCode 사용 기준으로 다음의 절차를 따른다. 

1. `tsconfig.node.json` 및 `tsconfig.web.json` 파일에서 `"moduleResolution"` 의 값을 `"bundler"` 로 설정한다. 
2. `.ts` 또는 `.tsx` 파일 아무거나 하나를 선택하여 그 코드가 화면에 뜬 상태로 만든다.
3. 그러면 VSCode 창의 우측 하단에 “TypeScript JSX”라는 텍스트가 새로 뜰 것이다. 그 바로 왼쪽에 `{}` 형태의 아이콘이 있다. 마우스를 그 아이콘 위에 둔다. 
    
    ![사진 2-1.](/images/2025-12-03/electron-vite-Trouble%20Shooting-'vitejs-plugin-react'%20%EB%AA%A8%EB%93%88%20%EB%98%90%EB%8A%94%20%ED%95%B4%EB%8B%B9%20%ED%98%95%EC%8B%9D%20%EC%84%A0%EC%96%B8%EC%9D%84%20%EC%B0%BE%EC%9D%84%20%EC%88%98%20%EC%97%86%EC%8A%B5%EB%8B%88%EB%8B%A4/2.png)
    
    사진 2-1.
    
4. 그러면 위 사진과 같은 목록이 뜰 것이다. 위 사진과 같이 “TypeScript 버전”이 5 미만일 경우 오른쪽의 “버전 선택”을 클릭한다. 
5. 그러면 아래 사진과 같이 VScode 에디터 맨 위에 다음과 같은 목록들이 뜰 것이다. 처음에는 “VS Code의 버전 사용”으로 선택되어 있을 것이다. 이를 “작업 영역 버전 사용” 항목을 클릭하여 변경한다. 
    
    ![3.png](/images/2025-12-03/electron-vite-Trouble%20Shooting-'vitejs-plugin-react'%20%EB%AA%A8%EB%93%88%20%EB%98%90%EB%8A%94%20%ED%95%B4%EB%8B%B9%20%ED%98%95%EC%8B%9D%20%EC%84%A0%EC%96%B8%EC%9D%84%20%EC%B0%BE%EC%9D%84%20%EC%88%98%20%EC%97%86%EC%8A%B5%EB%8B%88%EB%8B%A4/3.png)
    

그러면 다음의 사항들이 모두 해결된다.

1. `electron.vite.config.js` 파일의 `import react from '@vitejs/plugin-react'` 코드 부분에 빨간 줄이 사라진다.
2. `npm run build` 를 통한 앱 빌드시 더 이상 에러가 발생하지 않고 잘 빌드된다. 
3. 뿐만 아니라, `"moduleResolution"` 값을 `"bundler"` 로 하더라도 `App.tsx` 와 같은 파일 내 코드들에 빨간 줄이 생기지 않게 된다. 

Typescript 5 버전부터 `"moduleResolution": "bundler"` 라는 속성값을 지원하기 시작했다. 기존에는 `"node"` , `"nodenext"` 와 같은 속성값들이 있는데 이 속성값들은 대부분 node.js의 엄격한 ESM import 규칙들을 따르고 있다. 반면 `"bundler"` 속성값은 해당 프로젝트에서 이미 webpack, Vite 등의 외부 번들러를 사용하고 있다고 가정하고 그 번들러에서 제공하는 좀 더 유연한 ESM import 규칙을 사용하는 방식이며, 이러한 사항을 Typescript에게 인식시켜주는 값이라 할 수 있다.

한 편, `"moduleResolution": "bundler"` 를 적용하더라도 필자의 경우 작업 영역 내 `node_modules` 폴더 내부에 있는 타입스크립트 버전을 따르는 것이 아닌 VSCode에서의 버전을 사용하고 있었다. 그리고 VSCode에서는 Typescript 4 버전대를 사용하고 있었다. 그러니 `"moduleResolution": "bundler"` 값을 사용하고 있더라도 해당 기능을 제대로 활용할 수 없었던 것이었다. 

이로써 `@vitejs/plugin-react` 의 버전을 바꾸지 않더라도 위에서 설명한 과정을 따라하면 관련 문제들을 좀 더 근본적으로 해결할 수 있게 되었다. 처음에는 해당 플러그인과 electron-vite간의 버전 호환성 문제 때문인가 했는데, VSCode의 문제였다니 조금 의외였다. 

---

References

[1] `@vitejs/plugin-react` 버전별 changelog

[https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md)

[2] [[TIL] tsconfig와 moduleResolution](https://velog.io/@qorgkr26/tsconfig-module-resolution)

[3] [Documentation - Modules - Theory](https://www.typescriptlang.org/docs/handbook/modules/theory.html#module-resolution)

[4] [TypeScript 5.0: new mode bundler & ESM](https://dev.to/ayc0/typescript-50-new-mode-bundler-esm-1jic)