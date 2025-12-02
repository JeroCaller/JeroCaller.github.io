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

---

References

[1] `@vitejs/plugin-react` 버전별 changelog

[https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/CHANGELOG.md)