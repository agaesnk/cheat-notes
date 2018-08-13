# ngx-markdown

+++

ngx-markdown is an [Angular](https://angular.io/) library that uses [marked](https://github.com/chjj/marked) to parse markdown to html combined with [Prism.js](http://prismjs.com/) for syntax highlight.

 

 ## Setup

+++

### Install `marked`

A markdown parser and compiler.  As the library is using [marked](https://github.com/chjj/marked) parser you will need to add `node_modules/marked/lib/marked.js` to your application:

`npm install marked --save`



Added to node._modules:

+ In `client/angular.json`:

```javascript
  "scripts": [
              ...
              "node_modules/marked/lib/marked.js",
              ...
            ]
```



**NOTE**: Marked does not [sanitize](https://marked.js.org/#/USING_ADVANCED.md#options) the output HTML by default!



### Install `ngx-markdown`

ngx-markdown is an [Angular](https://angular.io/) library that uses [marked](https://github.com/chjj/marked) to parse markdown to html combined with [Prism.js](http://prismjs.com/) for syntax highlight.



To add ngx-markdown library to your `package.json` use the following command. 

`npm install ngx-markdown —save`



### Install `Prism.js`

Syntax highlight is **optional**, skip this step if you are not planning to use it. To activate [Prism.js](http://prismjs.com/) syntax highlight you will need to include:

+ install Prism from npm
+ `$ npm install prismjs —save`



- prism.js core library - `node_modules/prismjs/prism.js` file
- a highlight css theme - from `node_modules/prismjs/themes` directory
- desired code language syntax files - from `node_modules/prismjs/components` directory



Download your css/js files from here: https://prismjs.com/download.html



- In `client/angular.json`:

```javascript
"styles": [
    "src/styles.css",
    "node_modules/prismjs/themes/prism.css"
],
    "scripts": [
        "node_modules/prismjs/prism.js",
        "node_modules/prismjs/components/prism-javascript.min.js", 
        "node_modules/prismjs/components/prism-css.min.js"
    ]
```



## Main application module

+++

You must import `MarkdownModule` inside your main application module (usually named AppModule) with `forRoot` to be able to use `markdown` component and/or directive.



- In `app.module.ts`:

```javascript
import { NgModule } from '@angular/core';
import { MarkdownModule } from 'ngx-markdown';

import { AppComponent } from './app.component';

@NgModule({
  imports: [
+   MarkdownModule.forRoot(),
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent],
})
export class AppModule { }
```



If you want to use the `[src]` attribute to directly load a remote file, in order to keep only one instance of `HttpClient`and avoid issues with interceptors, you also have to provide `HttpClient`:

```javascript
imports: [
HttpClientModule,
MarkdownModule.forRoot({ loader: HttpClient }),
],
```



### MarkedOptions

Optionaly, markdown parsing can be configured by passing [MarkedOptions](https://marked.js.org/#/USING_ADVANCED.md#options) to the `forRoot` method of `MarkdownModule`.

Imports:

```
import { MarkdownModule, MarkedOptions } from 'ngx-markdown';
```

Default options:

```
// using default options
MarkdownModule.forRoot(),
```

Custom options and passing `HttpClient` to use `[src]` attribute:

```javascript
// using specific options with ValueProvider and passing HttpClient
MarkdownModule.forRoot({
  loader: HttpClient, // optional, only if you use [src] attribute
  markedOptions: {
    provide: MarkedOptions,
    useValue: {
      gfm: true,
      tables: true,
      breaks: false,
      pedantic: false,
      sanitize: false,
      smartLists: true,
      smartypants: false,
    },
  },
}),
```





### Other application modules

Use `forChild` when importing `MarkdownModule` into other application modules to allow you to use the same parser configuration accross your application.

```javascript
import { NgModule } from '@angular/core';
+ import { MarkdownModule } from 'ngx-markdown';

import { HomeComponent } from './home.component';

@NgModule({
  imports: [
+   MarkdownModule.forChild(),
  ],
  declarations: [HomeComponent],
})
export class HomeModule { }
```







## Usage

`ngx-markdown` provides different approaches to help you parse markdown to your application depending of your needs.

### Component

You can use `markdown` component to either parse static markdown directly from your html markup, load the content from a remote url using `src` property or bind a variable to your component using `data` property. You can get a hook on load complete using `load` output event property or on loading error using `error` output event property.

```
<!-- static markdown -->
<markdown ngPreserveWhitespaces>
  # Markdown
</markdown>

<!-- loaded from remote url -->
<markdown [src]="'path/to/file.md'" (load)="onLoad($event)" (error)="onError($event)"></markdown>

<!-- variable binding -->
<markdown [data]="markdown"></markdown>
```

### Directive

The same way the component works, you can use `markdown` directive to accomplish the same thing.

```
<!-- static markdown -->
<div markdown ngPreserveWhitespaces>
  # Markdown
</div>

<!-- loaded from remote url -->
<div markdown [src]="'path/to/file.md'" (load)="onLoad($event)" (error)="onError($event)"></div>

<!-- variable binding -->
<div markdown [data]="markdown"></div>
```

### Pipe

Using `markdown` pipe to transform markdown to HTML allow you to chain pipe transformations and will update the DOM when value changes.

```
<!-- chain `language` pipe with `markdown` pipe to convert typescriptMarkdown variable content -->
<div [innerHTML]="typescriptMarkdown | language : 'typescript' | markdown"></div>
```

### Service

You can use `MarkdownService` to have access to markdown parser and syntax highlight methods.

```
import { Component, OnInit } from '@angular/core';
import { MarkdownService } from 'ngx-markdown';

@Component({ ... })
export class ExampleComponent implements OnInit() {
  constructor(private markdownService: MarkdownService) { }

  ngOnInit() {
    // outputs: <p>I am using <strong>markdown</strong>.</p>
    console.log(this.markdownService.compile('I am using __markdown__.'));
  }
}
```

## Renderer

Tokens can be render in a custom manner by either...

- providing the `renderer` property with the `MarkedOptions` when importing `MarkdownModule.forRoot()` into your main application module (see [Configuration](https://github.com/jfcere/ngx-markdown#markedoptionsrenderer) section)
- using `MarkdownService` exposed `renderer`

Here is an example of overriding the default heading token rendering through `MarkdownService` by adding an embedded anchor tag like on GitHub:

```
import { Component, OnInit } from '@angular/core';
import { MarkdownService } from 'ngx-markdown';

@Component({
  selector: 'app-example',
  template: '<markdown># Heading</markdown>',
})
export class ExampleComponent implements OnInit() {
  constructor(private markdownService: MarkdownService) { }

  ngOnInit() {
    this.markdownService.renderer.heading = (text: string, level: number) => {
      const escapedText = text.toLowerCase().replace(/[^\w]+/g, '-');
      return '<h' + level + '>' +
               '<a name="' + escapedText + '" class="anchor" href="#' + escapedText + '">' +
                 '<span class="header-link"></span>' +
               '</a>' + text +
             '</h' + level + '>';
    };
  }
}
```

This code will output the following HTML:

```
<h1>
  <a name="heading" class="anchor" href="#heading">
    <span class="header-link"></span>
  </a>
  Heading
</h1>
```

> Follow official [marked.renderer](https://github.com/chjj/marked#block-level-renderer-methods) documentation for the list of tokens that can be overriden.

## Syntax highlight

When using static markdown you are responsible to provide the code block with related language.

```
<markdown ngPreserveWhitespaces>
+  ```typescript
    const myProp: string = 'value';
+  ```
</markdown>
```

When using remote url ngx-markdown will use file extension to automatically resolve the code language.

```
<!-- will use html highlights -->
<markdown [src]="'path/to/file.html'"></markdown>

<!-- will use php highlights -->
<markdown [src]="'path/to/file.php'"></markdown>
```

When using variable binding you can optionally use `language` pipe to specify the language of the variable content (default value is markdown when pipe is not used).

```
<markdown [data]="markdown | language : 'typescript'"></markdown>
```
