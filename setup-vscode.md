# Setup VS Code untuk Go dan Web Development

Ada beberapa langkah yang harus dilakukan, yaitu :

1. Install NPM
2. Install Gulp dan ekstensinya
3. Install Visual Studio Code
7. Setting Visual Studio Code
8. Atur task untuk Visual Studio Code

### Install NPM

NPM adalah _package manager_ untuk Javascript yang dibundle bersama dengan Node.js. Untuk menggunakannya, install `nodejs`.

### Install Gulp dan ekstensinya

Gulp adalah sistem untuk melakukan otomatisasi sejumlah proses dalam satu build. Untuk menggunakannya, install `gulp` melalui `npm`. Beberapa ekstensi yang penting, di antaranya adalah :

1. `gulp-sass`
2. `gulp-autoprefixer`
3. `gulp-clean-css`
4. `gulp-server-livereload`

Berikut adalah contoh `gulpfile.js` :

```javascript
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    cleanCSS = require('gulp-clean-css'),
    server = require('gulp-server-livereload');

gulp.task('sass', function () {
    gulp.src('sass/**/*.scss')
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

gulp.task('default', ['webserver'], function () {
    gulp.watch('sass/**/*.scss', ['sass']);
});
```

### Install Visual Studio Code

Download Visual Studio Code di [halaman resmi](https://code.visualstudio.com/download), lalu pilih sesuai OS yang digunakan, atau gunakan repository yang tersedia. Selanjutnya install ekstensi yang diinginkan. Beberapa ekstensi yang umum digunakan adalah :

1. [Go](https://marketplace.visualstudio.com/items?itemName=lukehoban.Go)
2. [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)
3. [Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)
4. [Git History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory)
5. [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
6. [CSS Comb](https://marketplace.visualstudio.com/items?itemName=mrmlnc.vscode-csscomb)
7. [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
8. [Minify](https://marketplace.visualstudio.com/items?itemName=HookyQR.minify)
9. [SCSS IntelliSense](https://marketplace.visualstudio.com/items?itemName=mrmlnc.vscode-scss)
10. [Icon Fonts](https://marketplace.visualstudio.com/items?itemName=idleberg.icon-fonts)

Agar nyaman bekerja di malam hari, install theme yang bersifat agak gelap. Beberapa theme yang direkomendasikan adalah :

1. [Monokai-Contrast Theme](https://marketplace.visualstudio.com/items?itemName=gerane.Theme-Monokai-Contrast)
2. [Dracula Syntax Theme](https://marketplace.visualstudio.com/items?itemName=dracula-theme.theme-dracula)

Selanjutnya, ada beberapa icon yang direkomendasikan, yaitu :

1. [Material Icon Theme](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)
2. [VSCode Icons](https://marketplace.visualstudio.com/items?itemName=PKief.material-icon-theme)

### Setting Visual Studio Code

Untuk user setting, diatur sebagai berikut :

```json
{
    // App setting
    "window.zoomLevel": 0,
    "editor.fontFamily": "Ubuntu Mono",
    "editor.fontSize": 15,
    "editor.wordBasedSuggestions": false,
    "editor.scrollBeyondLastLine": false,
    "telemetry.enableTelemetry": false,
    "editor.formatOnSave": true,
    "git.confirmSync": false,
    "vsicons.dontShowNewVersionMessage": true,

    // CSS Comb settings
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
        "sort-order": ["..."],
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

    // Go settings
    "go.formatTool": "gofmt",
    "go.useCodeSnippetsOnFunctionSuggest": true,
    "go.formatFlags": [
        "-s"
    ],

    // Beautify settings
    "beautify.language": {
        "js": {
            "type": [
                "javascript",
                "json"
            ],
            "filename": [
                ".jshintrc",
                ".jsbeautify"
            ]
        },
        "css": [
            "css",
            "scss"
        ],
        "html": [
            "htm",
            "html"
        ]
    },

    // HTML settings
    "html.format.wrapLineLength": 0,
    "html.format.extraLiners": "head, body, /html"
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
    },
    {
        "key": "ctrl+shift+.",
        "command": "editor.emmet.action.nextEditPoint",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+,",
        "command": "editor.emmet.action.previousEditPoint",
        "when": "editorTextFocus"
    }
]
```

### Atur task untuk Visual Studio Code

Task bisa diatur sesuai kebutuhan, salah satu contohnya adalah :

```json
{
    "version": "0.1.0",
    "_runner": "terminal",
    "showOutput": "always",
    "suppressTaskName": true,
    "tasks": [{
            "taskName": "Liveserver",
            "command": "gulp --gulpfile ./view/gulpfile.js",
            "isBackground": true,
            "isShellCommand": true
        },
        {
            "taskName": "Compile SASS",
            "command": "gulp --gulpfile ./view/gulpfile.js sass",
            "isShellCommand": true
        },
        {
            "taskName": "Build Go",
            "command": "go generate && go build -v",
            "isShellCommand": true
        },
        {
            "taskName": "Run Go",
            "command": "go generate && go build -v && ./executable",
            "isBackground": true,
            "isBuildCommand": true,
            "isShellCommand": true
        }
    ]
}
```