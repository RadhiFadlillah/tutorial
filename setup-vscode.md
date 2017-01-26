# Setup VS Code untuk Go dan Web Development

### Install NPM

NPM adalah _package manager_ untuk Javascript yang dibundle bersama dengan Node.js. Install Node.js dengan perintah :

```bash
curl -sL https://deb.nodesource.com/setup_7.x | sudo -E bash -
sudo apt-get update
sudo apt-get install -y nodejs
sudo apt-get install build-essential
```

### Install VS Code

Download Visual Studio Code di [halaman resmi](https://code.visualstudio.com/download), lalu pilih sesuai OS yang digunakan.

### Install _extension_

Beberapa ekstensi yang digunakan adalah :

1. [Go](https://marketplace.visualstudio.com/items?itemName=lukehoban.Go)
2. [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)
3. [Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)
4. [Git History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)
5. [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
6. [HTML Snippets](https://marketplace.visualstudio.com/items?itemName=abusaidm.html-snippets)
7. [Vue 2 Snippets](https://marketplace.visualstudio.com/items?itemName=hollowtree.vue-snippets)
8. [CSS Comb](https://marketplace.visualstudio.com/items?itemName=mrmlnc.vscode-csscomb)

### Install icon

Icon yang digunakan adalah [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme).

### Install theme

Beberapa theme yang direkomendasikan adalah :

1. [Monokai-Contrast Theme](https://marketplace.visualstudio.com/items?itemName=gerane.Theme-Monokai-Contrast)
2. [Dracula Syntax Theme](https://marketplace.visualstudio.com/items?itemName=dracula-theme.theme-dracula)

### Setting VS Code

Untuk user setting, diatur sebagai berikut :

```json
{
    "window.zoomLevel": 0,
    "editor.fontFamily": "Ubuntu Mono",
    "editor.fontSize": 15,
    "editor.wordBasedSuggestions": false,
    "editor.scrollBeyondLastLine": false,
    "telemetry.enableTelemetry": false,
    "editor.formatOnSave": true,
    "csscomb.preset": {
        "remove-empty-rulesets": true,
        "always-semicolon": true,
        "color-case": "upper",
        "block-indent": "\t",
        "color-shorthand": true,
        "element-case": "lower",
        "eof-newline": true,
        "leading-zero": true,
        "quotes": "double",
        "sort-order-fallback": "abc",
        "space-before-colon": "",
        "space-after-colon": " ",
        "space-before-combinator": " ",
        "space-after-combinator": " ",
        "space-between-declarations": "\n",
        "space-before-opening-brace": " ",
        "space-after-opening-brace": "\n",
        "space-after-selector-delimiter": " ",
        "space-before-selector-delimiter": "",
        "space-before-closing-brace": "\n",
        "strip-spaces": true,
        "unitless-zero": true,
        "vendor-prefix-align": true
    },
    "csscomb.formatOnSave": true,
    "go.formatTool": "gofmt",
    "go.useCodeSnippetsOnFunctionSuggest": true,
    "go.formatFlags": [
        "-s"
    ]
}
```

Lalu atur keyboard shortcut :

```json
[{
        "key": "ctrl+k ctrl+t",
        "command": "workbench.action.tasks.runTask"
    },
    {
        "key": "ctrl+k ctrl+b",
        "command": "HookyQR.beautifyFile"
    },
    {
        "key": "ctrl+k ctrl+q",
        "command": "workbench.action.tasks.terminate"
    },
    {
        "key": "ctrl+shift+down",
        "command": "editor.action.copyLinesDownAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+alt+down",
        "command": "editor.action.insertCursorBelow",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+up",
        "command": "editor.action.copyLinesUpAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+alt+up",
        "command": "editor.action.insertCursorAbove",
        "when": "editorTextFocus"
    }
]
```

### Konfigurasi untuk web development

Untuk mempermudah pengembangan web, digunakan `liveserver` dan `livereload` untuk menampilkan hasil modifikasi file HTML, JS dan CSS. Selain itu, digunakan SASS _preprocessor_ karena lebih nyaman digunakan dibanding CSS biasa. 

Agar `vscode` dapat menggunakan `livereload` dan `sass` setiap kali merubah file, digunakan `gulp`. Pertama-tama, install `gulp` dan tool lainnya menggunakan `npm` :

```bash
sudo npm install -g gulp gulp-autoprefixer gulp-clean-css gulp-sass gulp-server-livereload
```

Setelah itu, buat `gulpfile.js` di root direktori dengan isi sebagai berikut :

```js
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    cleanCSS = require('gulp-clean-css'),
    server = require('gulp-server-livereload');

gulp.task('sass', function () {
    gulp.src('scss/**/*.scss')
        .pipe(sass().on('error', sass.logError))
        .pipe(autoprefixer({
            browsers: ['last 2 versions'],
            cascade: false
        }))
        .pipe(cleanCSS())
        .pipe(gulp.dest('./css'));
});

gulp.task('webserver', function () {
    gulp.src('./')
        .pipe(server({
            livereload: true,
            directoryListing: true,
            open: true,
            log: 'debug',
            clientConsole: true
        }));
});

gulp.task('default', ['sass', 'webserver'], function () {
    gulp.watch('scss/**/*.scss', ['sass']);
});
```

### Konfigurasi VS Code task

Task bisa diatur sesuai kebutuhan, salah satu contohnya adalah :

```json
{
    "version": "0.1.0",
    "command": "sh",
    "args": [
        "-c"
    ],
    "isShellCommand": true,
    "showOutput": "always",
    "suppressTaskName": true,
    "tasks": [
        {
            "taskName": "Gulp",
            "args": [
                "gulp"
            ],
            "isWatching": true
        },
        {
            "taskName": "build",
            "args": [
                "go build -v"
            ],
            "isBuildCommand": true
        }
    ]
}
```