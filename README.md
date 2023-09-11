Electron is a framework for building desktop applications using JavaScript, HTML, and CSS.
By embedding Chromium and Node.js into a single binary file. 
Electron allows you to create cross-platform apps that work on Windows, macOS, and Linux with a single JavaScript codebase.
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Electron is a native wrapper layer for web apps and is run in a Node.js environment.
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Therefore, you must be generally familiar with Node and front-end web development basics.

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
To begin developing an Electron app, you need to install the Node.js runtime and its bundled npm package manager onto your system. 
We recommend that you use the latest long-term support (LTS) version.
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
To check that Node.js was installed correctly, you can use the -v flag when running the node and npm commands. These should print out the installed versions.
commad ----- npm version    ( here you can check the version of node and npm modules) ;
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Main Topics -----  Building First project
/////////////////////////////////////
In this part you will learn how to set up your Electron project and write a minimal starter application. By the end of this section, you should be able to run a working Electron app in development mode from your terminal.

To build your first Electron application: Electron apps are scaffolded using npm, with the package.json file as an entry point. Start by creating a folder and initializing an npm package within it with npm init.


Step -1 (use cmd or VsCode Terminal )---> Initializing your npm project

command 1 ( making the folder ) --> mkdir my-electron-app && cd my-electron-app
command 2 ( installing node modules in the folder ) --> npm init

This command will prompt you to configure some fields in your package.json. There are a few rules to follow for the purposes of this tutorial:

entry point should be main.js (you will be creating that file soon).

Then, install Electron into your app's devDependencies, which is the list of external development-only package dependencies not required in production.

command 3 --> npm install electron --save-dev


{
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!",
  "main": "main.js",
  "scripts": {
    "start": "electron .", ---------------> (this should be included so npm can work otherwise it'll show error)
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Jane Doe",
  "license": "MIT",
  "devDependencies": {
    "electron": "23.1.3"
  }
}

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
Main Topics -----  Loading a web page into a BrowserWindow

In Electron, each window displays a web page that can be loaded either from a local HTML file or a remote web address. For this example, you will be loading in a local file. Start by creating a barebones web page in an index.html file in the root folder of your project:

index.html file code below 
///////////////////////////////////////////////////////
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <meta
      http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <meta
      http-equiv="X-Content-Security-Policy"
      content="default-src 'self'; script-src 'self'"
    />
    <title>Hello from Electron renderer!</title>
  </head>
  <body>
    <h1>Hello from Electron renderer!</h1>
    <p>👋</p>
  </body>
</html>
///////////////////////////////////////////////////////
Now that you have a web page, you can load it into an Electron BrowserWindow. Replace the contents of your main.js file with the following code. We will explain each highlighted block separately.
///////////////////////////////////////////////////////
const { app, BrowserWindow } = require('electron')

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })

  win.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()
});
///////////////////////////////////////////////////////
In the first line, we are importing two Electron modules with CommonJS module syntax:

app, which controls your application's event lifecycle.
BrowserWindow, which creates and manages app windows.

const { app, BrowserWindow } = require('electron')

///////////////////////////////////////////////////////
The createWindow() function loads your web page into a new BrowserWindow instance:

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600
  })

  win.loadFile('index.html')
}
///////////////////////////////////////////////////////
Calling your function when the app is ready

app.whenReady().then(() => {
  createWindow()
});
///////////////////////////////////////////////////////
when you'll make progress this far , a pop of width and height defined will appear on your screen so try it before moving forward
///////////////////////////////////////////////////////
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') app.quit()
})

///////////////////////////////////////////////////////
Main Topics -----  Using Preload Scripts
///////////////////////////////////////////////////////
Learning goals

In this part , you will learn what a preload script is and how to use one to securely expose privileged APIs into the renderer process. You will also learn how to communicate between main and renderer processes with Electron's inter-process communication (IPC) modules.

What is a preload script?
Electron's main process is a Node.js environment that has full operating system access. On top of Electron modules, you can also access Node.js built-ins, as well as any packages installed via npm. On the other hand, renderer processes run web pages and do not run Node.js by default for security reasons.

To bridge Electron's different process types together, we will need to use a special script called a preload.


Preload scripts are injected before a web page loads in the renderer, similar to a Chrome extension's content scripts.
///////////////////////////////////////////////////////
const { contextBridge } = require('electron')

contextBridge.exposeInMainWorld('versions', {
  node: () => process.versions.node,
  chrome: () => process.versions.chrome,
  electron: () => process.versions.electron
  // we can also expose variables, not just functions
})
///////////////////////////////////////////////////////
To attach this script to your renderer process, pass its path to the webPreferences.preload option in the BrowserWindow constructor:

const { app, BrowserWindow } = require('electron')
const path = require('node:path')

const createWindow = () => {
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js')
    }
  })

  win.loadFile('index.html')
}

app.whenReady().then(() => {
  createWindow()
})
///////////////////////////////////////////////////////
Package and distribute your application
The fastest way to distribute your newly created app is using Electron Forge.

1. Add Electron Forge as a development dependency of your app, and use its import command to set up Forge's scaffolding:
npm install --save-dev @electron-forge/cli
npx electron-forge import

✔ Checking your system
✔ Initializing Git Repository
✔ Writing modified package.json file
✔ Installing dependencies
✔ Writing modified package.json file
✔ Fixing .gitignore

We have ATTEMPTED to convert your app to be in a format that electron-forge understands.

Thanks for using "electron-forge"!!!

2. Create a distributable using Forge's make command:
npm run make
///////////////////////////////////////////////////////
Advanced Installation Instructions

To install prebuilt Electron binaries, use npm. The preferred method is to install Electron as a development dependency in your app:

npm install electron --save-dev
///////////////////////////////////////////////////////
Customization

If you want to change the architecture that is downloaded (e.g., ia32 on an x64 machine), you can use the --arch flag with npm install or set the npm_config_arch environment variable:

npm install --arch=ia32 electron


In addition to changing the architecture, you can also specify the platform (e.g., win32, linux, etc.) using the --platform flag:

npm install --platform=win32 electron
