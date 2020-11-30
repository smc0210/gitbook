---
description: command snippet
---

# Command

## NPM

```bash
# package install
npm install [package_name]

npm -i
```

## Yarn

### Basic Command

project init

```bash
yarn init
```

package install

```bash
yarn add [package]
yarn add [package]@[version]
yarn add [package]@tag
```

Adding a dependency to different categories of dependencies

```bash
yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional
```

Upgrading a dependency

```bash
yarn upgrade [package]
yarn upgrade [package]@[version]
yarn upgrade [package]@[tag]
```

Removing a dependency

```bash
yarn remove [package]
```

Installing all the dependencies of project \(npm과 동일하게 package.json 사용\)

```bash
yarn
```

or

```bash
yarn install
```

### CLI

`yarn run` `package.json` 파일에 있는 `scripts` 영역에 정의 되어있는 커맨드 실행

{% code title="package.json" %}
```javascript
{
  "name": "my-package",
  "scripts": {
    "build": "babel src -d lib",
    "test": "jest"
  }
}
```
{% endcode %}

```bash
yarn run test

yarn run test -o --watch
```

