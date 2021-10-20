# ELECTRON

<br><br><br>
<hr>
<br><br><br>

# Create app

create `package.json`

```json
{
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!",
  "main": "main.js",
  "author": "Jane Doe",
  "license": "MIT"
}
```

install package

```sh
npm install --save-dev electron
```

add start command in `package.json`

```json
{
  "scripts": {
    "start": "electron ."
  }
}
```

open your app in development mode

```sh
npm start
```