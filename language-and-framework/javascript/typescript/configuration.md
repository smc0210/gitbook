---
description: 'Typescript 설정, Lint, DefinitelyTyped'
---

# Configuration

## `tsconfig`

typescript 설정 파일 구문 및 예시

{% code title="tsconfig.json" %}
```javascript
{
    "compilerOptions": {
      "baseUrl": ".",
      "module": "commonjs",
      "target": "es5",
      "lib": ["es6", "dom", "esnext.asynciterable"],
      "sourceMap": true,
      "allowJs": true,
      "moduleResolution": "node",
      "rootDir": "src",
      "forceConsistentCasingInFileNames": true,
      "noImplicitReturns": true,
      "noImplicitThis": true,
      "noImplicitAny": false,
      "strictNullChecks": true,
      "suppressImplicitAnyIndexErrors": true,
      "noUnusedLocals": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "experimentalDecorators": true,
      "emitDecoratorMetadata": true
    },
    "exclude": [
      "node_modules",
      "build",
      "scripts",
      "acceptance-tests",
      "webpack",
      "jest",
      "src/setupTests.ts"
    ]
  }
```
{% endcode %}

## `tslint`

`typescript` 용 linter 지원 파일로 import 구문 알파벳 순 정렬, `console.log()`  작성시 지우라고 안내를 해주는등 다양한 옵션 들이 있다. 아래는 그 예시

{% code title="tslint.json" %}
```javascript
{
    "extends": ["tslint:recommended", "tslint-config-prettier"],
    "linterOptions": {
      "exclude": ["config/**/*.js", "node_modules/**/*."]
    },
    "rules": {
      "no-console": false,
      "member-access": false,
      "object-literal-sort-keys": false,
      "ordered-imports": true,
      "interface-name": false,
      "strict-null-checks": false
    },
    "rulesDirectory": []
  }
```
{% endcode %}

[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)

거의 모든 라이브러리의 type을 정의 해놓아서 프로젝트로 필요한 타입을 설치해서 사

```bash
npm install --save-dev @types/node
```

