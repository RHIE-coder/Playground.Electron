# :book:  What is Electron
 - 2013년 Github이 Atom을 만들기 위해 개발한 프레임워크
 - Node.js와 HTML/CSS/JS만 가지고 데스크톱 APP을 만들게 하는 프레임워크
 - 크로스 플랫폼

## :pencil2: _구조_

### - Main Process : Node.js

### - Renderer Process : Chromium(HTML/CSS/JS)

`IPC모듈`과 `remote`모듈을 이용해 두 프로세스간 통신

###### IPC (Inter-Process Communication)

```
프로세스 사이의 데이터 통신은 IPC를 통해서 한다.
프로세스 간 통신이라는 뜻
```
 - remote 모듈은 Main Process에서만 사용 가능한 API들을 Renderer Process가 이용할 수 있도록하는 모듈(중간자)


<br><br><br><br><hr>

# :book: My First Electron App Example

## :pencil2: _step 1_

```
mkdir my-electron-app && cd my-electron-app
npm init -y
npm install --save-dev electron
```
 - `package.json`

```js
{
    "scripts": {
        "start": "electron ."
  }
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!",
  "main": "main.js",
  "author": "Jane Doe",
  "license": "MIT"
}
```

<br><br>

## :pencil2: _step 2_

 - `index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <!-- https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <meta http-equiv="X-Content-Security-Policy" content="default-src 'self'; script-src 'self'">
    <title>Hello World!</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using Node.js <span id="node-version"></span>,
    Chromium <span id="chrome-version"></span>,
    and Electron <span id="electron-version"></span>.

    <!-- You can also require other files to run in this process -->
    <script src="./renderer.js"></script>
  </body>
</html>
```

 - `app.js`

```js
const { app, BrowserWindow } = require('electron')

function createWindow () {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    
    // load preload.js
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  win.loadFile('index.html')
}
/* 
In Electron, browser windows can only be created after the app module's ready event is fired

You can wait for this event by using the app.whenReady() API. Call createWindow() after whenReady() resolves its Promise.
    // app.whenReady().then(() => {
    //     createWindow()
    // })
*/

/*********** Manage Lifecycle ***********/
/* 
Quit the app when all windows are closed (Windows & Linux)

To implement this, listen for the app module's 'window-all-closed' event, and call app.quit() if the user is not on macOS (darwin).
*/
app.on('window-all-closed', function () {
  if (process.platform !== 'darwin') app.quit()
})

app.whenReady().then(() => {
  createWindow()

/* 
macOS apps generally continue running even without any windows open, and activating the app when no windows are available should open a new one.

call your existing createWindow() method if no browser windows are open.

Because windows cannot be created before the ready event, you should only listen for activate events after your app is initialized
*/
  app.on('activate', function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow()
  })
})
```

<br><br>

## :pencil2: _step 3_

 - `preload.js`

```js
// All of the Node.js APIs are available in the preload process.
// It has the same sandbox as a Chrome extension.
window.addEventListener('DOMContentLoaded', () => {
  const replaceText = (selector, text) => {
    const element = document.getElementById(selector)
    if (element) element.innerText = text
  }

  for (const dependency of ['chrome', 'node', 'electron']) {
    replaceText(`${dependency}-version`, process.versions[dependency])
  }
})
```

 - `renderer.js`

```js
// This file is required by the index.html file and will
// be executed in the renderer process for that window.
// No Node.js APIs are available in this process because
// `nodeIntegration` is turned off. Use `preload.js` to
// selectively enable features needed in the rendering
// process.
```

<br><br>

## :pencil2: _step 4_

Package and distribute your application​

The fastest way to distribute your newly created app is using Electron Forge.

### 1. Add Electron Forge as a development dependency of your app, and use its import command to set up Forge's scaffolding:

```
npm install --save-dev @electron-forge/cli
npx electron-forge import
```
###### npm vs npx

```
npm = Package Manager
npx = Package Runner
```

### 2. Create a distributable using Forge's make command:

```
npm run make
```

Electron Forge creates the out folder where your package will be located: `out` directory