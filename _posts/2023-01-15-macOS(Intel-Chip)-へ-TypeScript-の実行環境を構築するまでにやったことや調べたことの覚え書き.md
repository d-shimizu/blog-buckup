---
title: "macOS(Intel Chip) へ TypeScript の実行環境を構築するまでにやったことや調べたことの覚え書き"
date: 2023-01-15
slug: "macOS(Intel Chip) へ TypeScript の実行環境を構築するまでにやったことや調べたことの覚え書き"
category: blog
tags: [TypeScript]
---
<h2 id="はじめに">はじめに</h2>

<p>今年は Typescript を勉強してみようと思い、まずは環境構築をするところから何にもわからん状態だったので調べたことの覚え書きです。</p>

<h2 id="TypeScript">TypeScript</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%DE%A5%A4%A5%AF%A5%ED%A5%BD%A5%D5%A5%C8">マイクロソフト</a>によって開発されている <a class="keyword" href="http://d.hatena.ne.jp/keyword/Javascript">Javascript</a> 用の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%D7%A5%ED%A5%B0%A5%E9%A5%DF%A5%F3%A5%B0%B8%C0%B8%EC">プログラミング言語</a>。
静的型付けの機能を有しているのが特徴と言われていて作成したTypeScriptのコードを<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>することで<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>へ変換され、 AltJS と呼ばれたりもしますが最近はAltJSという単語をあまり聞かなくなったような気もします。
全然わかっていないし詳しいサイト等はたくさんあるのでここではあまり書きません。</p>

<ul>
<li><a href="https://www.typescriptlang.org/">TypeScript: JavaScript With Syntax For Types.</a></li>
<li><a href="https://github.com/microsoft/TypeScript">GitHub - microsoft/TypeScript: TypeScript is a superset of JavaScript that compiles to clean JavaScript output.</a></li>
</ul>


<h2 id="実行環境構築">実行環境構築</h2>

<p>TypeScript をインストールして実行環境を構築してみます。</p>

<ul>
<li><a href="https://www.typescriptlang.org/download">TypeScript: How to set up TypeScript</a></li>
<li><a href="https://github.com/microsoft/TypeScript#installing">GitHub - microsoft/TypeScript: TypeScript is a superset of JavaScript that compiles to clean JavaScript output.</a></li>
</ul>


<h3 id="環境">環境</h3>

<pre class="code shell" data-lang="shell" data-unlink>% sw_vers
ProductName:    macOS
ProductVersion: 12.6.2
BuildVersion:   21G320</pre>


<h3 id="Nodejsのインストール">Node.jsのインストール</h3>

<p>TypeScript のインストールは npm からインストールするため、Node.js が必要になります。
また、TypeScript <a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%E9">コンパイラ</a>自体が TypeScript で動くため、そこでサーバーサイドJS が必要なため Node.js が利用されるようです。</p>

<p>Node.js のインストール方法はいろいろあって、 <a href="https://github.com/hokaccha/nodebrew">nodebrew</a> とか <a href="https://github.com/nvm-sh/nvm">nvm</a> とか <a href="https://docs.volta.sh/guide/">volta</a> とかがあるようですが、どれが良いか詳しくわからないのと、とりあえず TypeScript を動かせる状態にしたいので、ここでは Homebrew でインストールします。</p>

<p>偶数バージョンが LTS なので、現時点で最新 LTS のバージョン 18 系をインストールします。</p>

<pre class="code shell" data-lang="shell" data-unlink>% brew install node@18</pre>


<p>インストールされました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% node -v
v18.13.0</pre>


<p>コマンドがないとか言われる場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B7%A5%F3%A5%DC%A5%EA%A5%C3%A5%AF%A5%EA%A5%F3%A5%AF">シンボリックリンク</a>を貼ります。</p>

<pre class="code shell" data-lang="shell" data-unlink>% brew link node@18</pre>


<p>同時に npm の <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a> ツールもインストールされます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npm -v
8.19.3</pre>




<pre class="code shell" data-lang="shell" data-unlink>% npx -v
8.19.3</pre>


<h3 id="TypeScript-のインストール">TypeScript のインストール</h3>

<p>npm コマンドが使えるようになったので、 TypeScript をインストールします。</p>

<p>まず適当に作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リを作成して移動します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% mkdir -p ~/workspace/typescript-practice</pre>


<p>npm の初期化を行います。
<code>package.json</code> が生成されます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npm init --yes
Wrote to /path/to/workspace/typescript-practice/package.json:

{
  &#34;name&#34;: &#34;typescript-practice&#34;,
  &#34;version&#34;: &#34;1.0.0&#34;,
  &#34;description&#34;: &#34;&#34;,
  &#34;main&#34;: &#34;index.js&#34;,
  &#34;scripts&#34;: {
    &#34;test&#34;: &#34;echo \&#34;Error: no test specified\&#34; &amp;&amp; exit 1&#34;
  },
  &#34;keywords&#34;: [],
  &#34;author&#34;: &#34;&#34;,
  &#34;license&#34;: &#34;ISC&#34;
}</pre>


<p>作業<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下でインストールコマンドを実行します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npm install typescript --save-dev</pre>


<p>インストールされました。
以下のようなファイル、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ構成になりました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% tree -L 2
.
├── node_modules
│   └── typescript
├── package-lock.json
└── package.json</pre>


<p><code>package.json</code> の中身を確認してみると、 <code>typescript</code> は <code>devDependencies</code> のところに追記されています。
<code>devDependencies</code> は、プログラムの実行に必要なパッケージではなく、開発のために必要なパッケージであることを表すもののようです。 <code>npm install --production</code> といったコマンドを実行した場合、<code>devDependencies</code> のパッケージはインストールされない、などの細かい制御に使われるもののようです。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">name</span>&quot;: &quot;<span class="synConstant">typescript-practice</span>&quot;,
  &quot;<span class="synStatement">version</span>&quot;: &quot;<span class="synConstant">1.0.0</span>&quot;,
  &quot;<span class="synStatement">description</span>&quot;: &quot;&quot;,
  &quot;<span class="synStatement">main</span>&quot;: &quot;<span class="synConstant">index.js</span>&quot;,
  &quot;<span class="synStatement">scripts</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">test</span>&quot;: &quot;<span class="synConstant">echo </span><span class="synSpecial">\&quot;</span><span class="synConstant">Error: no test specified</span><span class="synSpecial">\&quot;</span><span class="synConstant"> &amp;&amp; exit 1</span>&quot;
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">keywords</span>&quot;: <span class="synSpecial">[]</span>,
  &quot;<span class="synStatement">author</span>&quot;: &quot;&quot;,
  &quot;<span class="synStatement">license</span>&quot;: &quot;<span class="synConstant">ISC</span>&quot;,
  &quot;<span class="synStatement">devDependencies</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">typescript</span>&quot;: &quot;<span class="synConstant">^4.9.5</span>&quot;
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<p>これで <code>tcs</code> というコマンドが利用可能になります。
<code>npm install</code> 実行時に <code>-g</code> or <code>--global</code> オプションを付与していないので、<code>npm install</code> 実行した<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ配下に <code>node_modules</code> ができており、その配下にパッケージがインストールされていました。
コマンド類も以下のように <code>node_modules</code> 配下にあります。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ls node_modules/typescript/bin/
tsc     tsserver</pre>


<p>このままだとパスが通っていないので、これを実行するために npx というコマンドを組み合わせてコマンドを実行します。
npx は npm インストール時に一緒にインストールされたプログラムで、<code>node_modules</code> 配下のコマンドを実行してくれる<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%DE%A5%F3%A5%C9%A5%E9%A5%A4%A5%F3">コマンドライン</a>ツールのようです。</p>

<ul>
<li><a href="https://github.com/npm/cli/blob/latest/bin/npx">cli/npx at latest &middot; npm/cli &middot; GitHub</a></li>
</ul>


<p>以下のようになります。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npx tsc --version
Version 4.9.4</pre>


<h3 id="TypeScript-の設定">TypeScript の設定</h3>

<p>プロジェクト<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ直下で <code>tsc --init</code> コマンドを実行し、 <code>tsconfig.json</code> を生成します。
<code>tsconfig.json</code> は、中身に記載されている通り、TypeScriptの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%E9">コンパイラ</a>オプションが記載されています。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npx tsc --init

Created a new tsconfig.json with:
                                                                                                                     TS
  target: es2016
  module: commonjs
  strict: true
  esModuleInterop: true
  skipLibCheck: true
  forceConsistentCasingInFileNames: true


You can learn more at https://aka.ms/tsconfig</pre>


<p>初期状態の中身は下記のような状態になっていました。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">compilerOptions</span>&quot;: <span class="synSpecial">{</span>
    <span class="synError">/*</span> <span class="synError">Visit</span> <span class="synError">https</span>:<span class="synError">//aka.ms/tsconfig to read more about this file */</span>

    <span class="synError">/*</span> <span class="synError">Projects</span> <span class="synError">*/</span>
    <span class="synError">// &quot;incremental&quot;: true,                              /* Save .tsbuildinfo files to allow for incremental compilation of projects. */</span>
    <span class="synError">// &quot;composite&quot;: true,                                /* Enable constraints that allow a TypeScript project to be used with project references. */</span>
    <span class="synError">// &quot;tsBuildInfoFile&quot;: &quot;./.tsbuildinfo&quot;,              /* Specify the path to .tsbuildinfo incremental compilation file. */</span>
    <span class="synError">// &quot;disableSourceOfProjectReferenceRedirect&quot;: true,  /* Disable preferring source files instead of declaration files when referencing composite projects. */</span>
    <span class="synError">// &quot;disableSolutionSearching&quot;: true,                 /* Opt a project out of multi-project reference checking when editing. */</span>
    <span class="synError">// &quot;disableReferencedProjectLoad&quot;: true,             /* Reduce the number of projects loaded automatically by TypeScript. */</span>

    <span class="synError">/*</span> <span class="synError">Language</span> <span class="synError">and</span> <span class="synError">Environment</span> <span class="synError">*/</span>
    &quot;<span class="synStatement">target</span>&quot;: &quot;<span class="synConstant">es2016</span>&quot;,                                  <span class="synError">/*</span> <span class="synError">Set</span> <span class="synError">the</span> <span class="synError">JavaScript</span> <span class="synError">language</span> <span class="synError">version</span> <span class="synError">for</span> <span class="synError">emitted</span> <span class="synError">JavaScript</span> <span class="synError">and</span> <span class="synError">include</span> <span class="synError">compatible</span> <span class="synError">library</span> <span class="synError">declarations</span>. <span class="synError">*/</span>
    <span class="synError">// &quot;lib&quot;: [],                                        /* Specify a set of bundled library declaration files that describe the target runtime environment. */</span>
    <span class="synError">// &quot;jsx&quot;: &quot;preserve&quot;,                                /* Specify what JSX code is generated. */</span>
    <span class="synError">// &quot;experimentalDecorators&quot;: true,                   /* Enable experimental support for TC39 stage 2 draft decorators. */</span>
    <span class="synError">// &quot;emitDecoratorMetadata&quot;: true,                    /* Emit design-type metadata for decorated declarations in source files. */</span>
    <span class="synError">// &quot;jsxFactory&quot;: &quot;&quot;,                                 /* Specify the JSX factory function used when targeting React JSX emit, e.g. 'React.createElement' or 'h'. */</span>
    <span class="synError">// &quot;jsxFragmentFactory&quot;: &quot;&quot;,                         /* Specify the JSX Fragment reference used for fragments when targeting React JSX emit e.g. 'React.Fragment' or 'Fragment'. */</span>
    <span class="synError">// &quot;jsxImportSource&quot;: &quot;&quot;,                            /* Specify module specifier used to import the JSX factory functions when using 'jsx: react-jsx*'. */</span>
    <span class="synError">// &quot;reactNamespace&quot;: &quot;&quot;,                             /* Specify the object invoked for 'createElement'. This only applies when targeting 'react' JSX emit. */</span>
    <span class="synError">// &quot;noLib&quot;: true,                                    /* Disable including any library files, including the default lib.d.ts. */</span>
    <span class="synError">// &quot;useDefineForClassFields&quot;: true,                  /* Emit ECMAScript-standard-compliant class fields. */</span>
    <span class="synError">// &quot;moduleDetection&quot;: &quot;auto&quot;,                        /* Control what method is used to detect module-format JS files. */</span>

    <span class="synError">/*</span> <span class="synError">Modules</span> <span class="synError">*/</span>
    &quot;<span class="synStatement">module</span>&quot;: &quot;<span class="synConstant">commonjs</span>&quot;,                                <span class="synError">/*</span> <span class="synError">Specify</span> <span class="synError">what</span> <span class="synError">module</span> <span class="synError">code</span> <span class="synError">is</span> <span class="synError">generated</span>. <span class="synError">*/</span>
    <span class="synError">// &quot;rootDir&quot;: &quot;./&quot;,                                  /* Specify the root folder within your source files. */</span>
    <span class="synError">// &quot;moduleResolution&quot;: &quot;node&quot;,                       /* Specify how TypeScript looks up a file from a given module specifier. */</span>
    <span class="synError">// &quot;baseUrl&quot;: &quot;./&quot;,                                  /* Specify the base directory to resolve non-relative module names. */</span>
    <span class="synError">// &quot;paths&quot;: {},                                      /* Specify a set of entries that re-map imports to additional lookup locations. */</span>
    <span class="synError">// &quot;rootDirs&quot;: [],                                   /* Allow multiple folders to be treated as one when resolving modules. */</span>
    <span class="synError">// &quot;typeRoots&quot;: [],                                  /* Specify multiple folders that act like './node_modules/@types'. */</span>
    <span class="synError">// &quot;types&quot;: [],                                      /* Specify type package names to be included without being referenced in a source file. */</span>
    <span class="synError">// &quot;allowUmdGlobalAccess&quot;: true,                     /* Allow accessing UMD globals from modules. */</span>
    <span class="synError">// &quot;moduleSuffixes&quot;: [],                             /* List of file name suffixes to search when resolving a module. */</span>
    <span class="synError">// &quot;resolveJsonModule&quot;: true,                        /* Enable importing .json files. */</span>
    <span class="synError">// &quot;noResolve&quot;: true,                                /* Disallow 'import's, 'require's or '&lt;reference&gt;'s from expanding the number of files TypeScript should add to a project. */</span>

    <span class="synError">/*</span> <span class="synError">JavaScript</span> <span class="synError">Support</span> <span class="synError">*/</span>
    <span class="synError">// &quot;allowJs&quot;: true,                                  /* Allow JavaScript files to be a part of your program. Use the 'checkJS' option to get errors from these files. */</span>
    <span class="synError">// &quot;checkJs&quot;: true,                                  /* Enable error reporting in type-checked JavaScript files. */</span>
    <span class="synError">// &quot;maxNodeModuleJsDepth&quot;: 1,                        /* Specify the maximum folder depth used for checking JavaScript files from 'node_modules'. Only applicable with 'allowJs'. */</span>

    <span class="synError">/*</span> <span class="synError">Emit</span> <span class="synError">*/</span>
    <span class="synError">// &quot;declaration&quot;: true,                              /* Generate .d.ts files from TypeScript and JavaScript files in your project. */</span>
    <span class="synError">// &quot;declarationMap&quot;: true,                           /* Create sourcemaps for d.ts files. */</span>
    <span class="synError">// &quot;emitDeclarationOnly&quot;: true,                      /* Only output d.ts files and not JavaScript files. */</span>
    <span class="synError">// &quot;sourceMap&quot;: true,                                /* Create source map files for emitted JavaScript files. */</span>
    <span class="synError">// &quot;outFile&quot;: &quot;./&quot;,                                  /* Specify a file that bundles all outputs into one JavaScript file. If 'declaration' is true, also designates a file that bundles all .d.ts output. */</span>
    <span class="synError">// &quot;outDir&quot;: &quot;./&quot;,                                   /* Specify an output folder for all emitted files. */</span>
    <span class="synError">// &quot;removeComments&quot;: true,                           /* Disable emitting comments. */</span>
    <span class="synError">// &quot;noEmit&quot;: true,                                   /* Disable emitting files from a compilation. */</span>
    <span class="synError">// &quot;importHelpers&quot;: true,                            /* Allow importing helper functions from tslib once per project, instead of including them per-file. */</span>
    <span class="synError">// &quot;importsNotUsedAsValues&quot;: &quot;remove&quot;,               /* Specify emit/checking behavior for imports that are only used for types. */</span>
    <span class="synError">// &quot;downlevelIteration&quot;: true,                       /* Emit more compliant, but verbose and less performant JavaScript for iteration. */</span>
    <span class="synError">// &quot;sourceRoot&quot;: &quot;&quot;,                                 /* Specify the root path for debuggers to find the reference source code. */</span>
    <span class="synError">// &quot;mapRoot&quot;: &quot;&quot;,                                    /* Specify the location where debugger should locate map files instead of generated locations. */</span>
    <span class="synError">// &quot;inlineSourceMap&quot;: true,                          /* Include sourcemap files inside the emitted JavaScript. */</span>
    <span class="synError">// &quot;inlineSources&quot;: true,                            /* Include source code in the sourcemaps inside the emitted JavaScript. */</span>
    <span class="synError">// &quot;emitBOM&quot;: true,                                  /* Emit a UTF-8 Byte Order Mark (BOM) in the beginning of output files. */</span>
    <span class="synError">// &quot;newLine&quot;: &quot;crlf&quot;,                                /* Set the newline character for emitting files. */</span>
    <span class="synError">// &quot;stripInternal&quot;: true,                            /* Disable emitting declarations that have '@internal' in their JSDoc comments. */</span>
    <span class="synError">// &quot;noEmitHelpers&quot;: true,                            /* Disable generating custom helper functions like '__extends' in compiled output. */</span>
    <span class="synError">// &quot;noEmitOnError&quot;: true,                            /* Disable emitting files if any type checking errors are reported. */</span>
    <span class="synError">// &quot;preserveConstEnums&quot;: true,                       /* Disable erasing 'const enum' declarations in generated code. */</span>
    <span class="synError">// &quot;declarationDir&quot;: &quot;./&quot;,                           /* Specify the output directory for generated declaration files. */</span>
    <span class="synError">// &quot;preserveValueImports&quot;: true,                     /* Preserve unused imported values in the JavaScript output that would otherwise be removed. */</span>

    <span class="synError">/*</span> <span class="synError">Interop</span> <span class="synError">Constraints</span> <span class="synError">*/</span>
    <span class="synError">// &quot;isolatedModules&quot;: true,                          /* Ensure that each file can be safely transpiled without relying on other imports. */</span>
    <span class="synError">// &quot;allowSyntheticDefaultImports&quot;: true,             /* Allow 'import x from y' when a module doesn't have a default export. */</span>
    &quot;<span class="synStatement">esModuleInterop</span>&quot;: <span class="synConstant">true</span>,                             <span class="synError">/*</span> <span class="synError">Emit</span> <span class="synError">additional</span> <span class="synError">JavaScript</span> <span class="synError">to</span> <span class="synError">ease</span> <span class="synError">support</span> <span class="synError">for</span> <span class="synError">importing</span> <span class="synError">CommonJS</span> <span class="synError">modules</span>. <span class="synError">This</span> <span class="synError">enables</span> <span class="synError">'allowSyntheticDefaultImports'</span> <span class="synError">for</span> <span class="synError">type</span> <span class="synError">compatibility</span>. <span class="synError">*/</span>
    <span class="synError">// &quot;preserveSymlinks&quot;: true,                         /* Disable resolving symlinks to their realpath. This correlates to the same flag in node. */</span>
    &quot;<span class="synStatement">forceConsistentCasingInFileNames</span>&quot;: <span class="synConstant">true</span>,            <span class="synError">/*</span> <span class="synError">Ensure</span> <span class="synError">that</span> <span class="synError">casing</span> <span class="synError">is</span> <span class="synError">correct</span> <span class="synError">in</span> <span class="synError">imports</span>. <span class="synError">*/</span>

    <span class="synError">/*</span> <span class="synError">Type</span> <span class="synError">Checking</span> <span class="synError">*/</span>
    &quot;<span class="synStatement">strict</span>&quot;: <span class="synConstant">true</span>,                                      <span class="synError">/*</span> <span class="synError">Enable</span> <span class="synError">all</span> <span class="synError">strict</span> <span class="synError">type</span>-<span class="synError">checking</span> <span class="synError">options</span>. <span class="synError">*/</span>
    <span class="synError">// &quot;noImplicitAny&quot;: true,                            /* Enable error reporting for expressions and declarations with an implied 'any' type. */</span>
    <span class="synError">// &quot;strictNullChecks&quot;: true,                         /* When type checking, take into account 'null' and 'undefined'. */</span>
    <span class="synError">// &quot;strictFunctionTypes&quot;: true,                      /* When assigning functions, check to ensure parameters and the return values are subtype-compatible. */</span>
    <span class="synError">// &quot;strictBindCallApply&quot;: true,                      /* Check that the arguments for 'bind', 'call', and 'apply' methods match the original function. */</span>
    <span class="synError">// &quot;strictPropertyInitialization&quot;: true,             /* Check for class properties that are declared but not set in the constructor. */</span>
    <span class="synError">// &quot;noImplicitThis&quot;: true,                           /* Enable error reporting when 'this' is given the type 'any'. */</span>
    <span class="synError">// &quot;useUnknownInCatchVariables&quot;: true,               /* Default catch clause variables as 'unknown' instead of 'any'. */</span>
    <span class="synError">// &quot;alwaysStrict&quot;: true,                             /* Ensure 'use strict' is always emitted. */</span>
    <span class="synError">// &quot;noUnusedLocals&quot;: true,                           /* Enable error reporting when local variables aren't read. */</span>
    <span class="synError">// &quot;noUnusedParameters&quot;: true,                       /* Raise an error when a function parameter isn't read. */</span>
    <span class="synError">// &quot;exactOptionalPropertyTypes&quot;: true,               /* Interpret optional property types as written, rather than adding 'undefined'. */</span>
    <span class="synError">// &quot;noImplicitReturns&quot;: true,                        /* Enable error reporting for codepaths that do not explicitly return in a function. */</span>
    <span class="synError">// &quot;noFallthroughCasesInSwitch&quot;: true,               /* Enable error reporting for fallthrough cases in switch statements. */</span>
    <span class="synError">// &quot;noUncheckedIndexedAccess&quot;: true,                 /* Add 'undefined' to a type when accessed using an index. */</span>
    <span class="synError">// &quot;noImplicitOverride&quot;: true,                       /* Ensure overriding members in derived classes are marked with an override modifier. */</span>
    <span class="synError">// &quot;noPropertyAccessFromIndexSignature&quot;: true,       /* Enforces using indexed accessors for keys declared using an indexed type. */</span>
    <span class="synError">// &quot;allowUnusedLabels&quot;: true,                        /* Disable error reporting for unused labels. */</span>
    <span class="synError">// &quot;allowUnreachableCode&quot;: true,                     /* Disable error reporting for unreachable code. */</span>

    <span class="synError">/*</span> <span class="synError">Completeness</span> <span class="synError">*/</span>
    <span class="synError">// &quot;skipDefaultLibCheck&quot;: true,                      /* Skip type checking .d.ts files that are included with TypeScript. */</span>
    &quot;<span class="synStatement">skipLibCheck</span>&quot;: <span class="synConstant">true</span>                                 <span class="synError">/*</span> <span class="synError">Skip</span> <span class="synError">type</span> <span class="synError">checking</span> <span class="synError">all</span> .<span class="synError">d</span>.<span class="synError">ts</span> <span class="synError">files</span>. <span class="synError">*/</span>
  }
}
</pre>


<p>実行環境に合わせた tsconfig のパッケージが公開されているので、それをインストールします。</p>

<ul>
<li><a href="https://github.com/tsconfig/bases/">GitHub - tsconfig/bases: Hosts TSConfigs to extend in a TypeScript app, tuned to a particular runtime environment</a></li>
</ul>


<p>今回は Node.js 18 用の tsconfig インストールします。</p>

<pre class="code shel" data-lang="shel" data-unlink>% npm install --save-dev @tsconfig/node18</pre>


<p><code>package.json</code> は下記のようになりました。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">name</span>&quot;: &quot;<span class="synConstant">typescript-practice</span>&quot;,
  &quot;<span class="synStatement">version</span>&quot;: &quot;<span class="synConstant">1.0.0</span>&quot;,
  &quot;<span class="synStatement">main</span>&quot;: &quot;<span class="synConstant">index.js</span>&quot;,
  &quot;<span class="synStatement">scripts</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">test</span>&quot;: &quot;<span class="synConstant">echo </span><span class="synSpecial">\&quot;</span><span class="synConstant">Error: no test specified</span><span class="synSpecial">\&quot;</span><span class="synConstant"> &amp;&amp; exit 1</span>&quot;
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">keywords</span>&quot;: <span class="synSpecial">[]</span>,
  &quot;<span class="synStatement">author</span>&quot;: &quot;&quot;,
  &quot;<span class="synStatement">license</span>&quot;: &quot;<span class="synConstant">ISC</span>&quot;,
  &quot;<span class="synStatement">devDependencies</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">@tsconfig/node18</span>&quot;: &quot;<span class="synConstant">^1.0.1</span>&quot;,
    &quot;<span class="synStatement">typescript</span>&quot;: &quot;<span class="synConstant">^4.9.5</span>&quot;
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">description</span>&quot;: &quot;&quot;
<span class="synSpecial">}</span>
</pre>


<p><code>tsconfig.json</code> に以下を追記します。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">extends</span>&quot;: &quot;@<span class="synError">tsconfig</span>/<span class="synError">node18</span>/<span class="synError">tsconfig</span>.<span class="synError">json</span>&quot;
  &quot;<span class="synStatement">compilerOptions</span>&quot;: <span class="synSpecial">{</span>
    :
  <span class="synSpecial">}</span>
<span class="synSpecial">}</span>
</pre>


<p>これで <code>node_modules/@tsconfig/node18/tsconfig.json</code> が読み込まれるようになります。
<code>node_modules/@tsconfig/node18/tsconfig.json</code> は下記のようになっています。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">$schema</span>&quot;: &quot;<span class="synConstant">https://json.schemastore.org/tsconfig</span>&quot;,
  &quot;<span class="synStatement">display</span>&quot;: &quot;<span class="synConstant">Node 18</span>&quot;,

  &quot;<span class="synStatement">compilerOptions</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">lib</span>&quot;: <span class="synSpecial">[</span>&quot;<span class="synConstant">es2022</span>&quot;<span class="synSpecial">]</span>,
    &quot;<span class="synStatement">module</span>&quot;: &quot;<span class="synConstant">commonjs</span>&quot;,
    &quot;<span class="synStatement">target</span>&quot;: &quot;<span class="synConstant">es2022</span>&quot;,

    &quot;<span class="synStatement">strict</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">esModuleInterop</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">skipLibCheck</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">forceConsistentCasingInFileNames</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">moduleResolution</span>&quot;: &quot;<span class="synConstant">node</span>&quot;
  }
}
</pre>


<p>下記を参考に、<code>tsconfig.json</code> に追加の修正をします。</p>

<ul>
<li><a href="https://typescriptbook.jp/reference/tsconfig/tsconfig.json-settings">tsconfig.json&#x3092;&#x8A2D;&#x5B9A;&#x3059;&#x308B; | TypeScript&#x5165;&#x9580;&#x300E;&#x30B5;&#x30D0;&#x30A4;&#x30D0;&#x30EB;TypeScript&#x300F;</a></li>
<li><a href="https://www.typescriptlang.org/docs/handbook/tsconfig-json.html">TypeScript: Documentation - What is a tsconfig.json</a></li>
</ul>


<p>とりあえず下記のようにしてみました。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">extends</span>&quot;: &quot;<span class="synConstant">@tsconfig/node18/tsconfig.json</span>&quot;,
  &quot;<span class="synStatement">compilerOptions</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">sourceMap</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">outDir</span>&quot;: &quot;<span class="synConstant">./dist</span>&quot;,
    &quot;<span class="synStatement">rootDir</span>&quot;: &quot;<span class="synConstant">./src</span>&quot;,
    &quot;<span class="synStatement">moduleResolution</span>&quot;: &quot;<span class="synConstant">node</span>&quot;,
    &quot;<span class="synStatement">baseUrl</span>&quot;: &quot;<span class="synConstant">src</span>&quot;,
    &quot;<span class="synStatement">esModuleInterop</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">experimentalDecorators</span>&quot;: <span class="synConstant">true</span>,
    &quot;<span class="synStatement">emitDecoratorMetadata</span>&quot;: <span class="synConstant">true</span>
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">include</span>&quot;: <span class="synSpecial">[</span>&quot;<span class="synConstant">src/**/*</span>&quot;<span class="synSpecial">]</span>,
  &quot;<span class="synStatement">exclude</span>&quot;: <span class="synSpecial">[</span>&quot;<span class="synConstant">dist</span>&quot;, &quot;<span class="synConstant">node_modules</span>&quot;<span class="synSpecial">]</span>,
  &quot;<span class="synStatement">compileOnSave</span>&quot;: <span class="synConstant">false</span>
<span class="synSpecial">}</span>
</pre>


<h4 id="JavaScriptECMAScript-規格">※<a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/ECMAScript">ECMAScript</a>) 規格</h4>

<p>ここで出てきた <code>es2022</code> というのは、 <a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a>(<a class="keyword" href="http://d.hatena.ne.jp/keyword/ECMAScript">ECMAScript</a>) 規格の 2022 年版です。ES2022 として2022年6月22日に正式承認されました。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/ECMAScript">ECMAScript</a>(エクマ<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B9%A5%AF%A5%EA%A5%D7%A5%C8">スクリプト</a>)というのは、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ecma">Ecma</a> International という団体のもとで標準化された <a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a> の規格になります。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/ECMAScript">ECMAScript</a> 仕様は、<a class="keyword" href="http://d.hatena.ne.jp/keyword/Ecma">Ecma</a> International で <a class="keyword" href="http://d.hatena.ne.jp/keyword/ECMA">ECMA</a>-262 という規格番号で標準化されてるようです。</p>

<ul>
<li><a href="https://tc39.es/ecma262/">ECMAScript&reg; 2023 Language&nbsp;Specification</a></li>
</ul>


<h3 id="サンプル-TypeScript-ファイル作成コンパイルNodejs-での実行">サンプル TypeScript ファイル作成・<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>・Node.js での実行</h3>

<p>サンプルコードを作って動かしてみます。
プロジェクト<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C7%A5%A3%A5%EC%A5%AF%A5%C8">ディレクト</a>リ直下にいることを確認します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% pwd
/path/to/workspace/typescript-practice</pre>


<p>Node.js の型定義用のパッケージをインストールします。
これがないと、作成したコードを TypeScript で<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>する時に、 Console.log() とかを呼び出そうとしたらそこでエラーになります。</p>

<ul>
<li><a href="https://www.npmjs.com/package/@types/node">@types/node - npm</a></li>
</ul>


<pre class="code shell" data-lang="shell" data-unlink>% npm install --save-dev @types/node@18</pre>


<p><code>package.json</code> は下記のようになりました。</p>

<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>
  &quot;<span class="synStatement">name</span>&quot;: &quot;<span class="synConstant">typescript-practice</span>&quot;,
  &quot;<span class="synStatement">version</span>&quot;: &quot;<span class="synConstant">1.0.0</span>&quot;,
  &quot;<span class="synStatement">main</span>&quot;: &quot;<span class="synConstant">index.js</span>&quot;,
  &quot;<span class="synStatement">scripts</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">test</span>&quot;: &quot;<span class="synConstant">echo </span><span class="synSpecial">\&quot;</span><span class="synConstant">Error: no test specified</span><span class="synSpecial">\&quot;</span><span class="synConstant"> &amp;&amp; exit 1</span>&quot;
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">keywords</span>&quot;: <span class="synSpecial">[]</span>,
  &quot;<span class="synStatement">author</span>&quot;: &quot;&quot;,
  &quot;<span class="synStatement">license</span>&quot;: &quot;<span class="synConstant">ISC</span>&quot;,
  &quot;<span class="synStatement">devDependencies</span>&quot;: <span class="synSpecial">{</span>
    &quot;<span class="synStatement">@tsconfig/node18</span>&quot;: &quot;<span class="synConstant">^1.0.1</span>&quot;,
    &quot;<span class="synStatement">@types/node</span>&quot;: &quot;<span class="synConstant">^18.11.18</span>&quot;,
    &quot;<span class="synStatement">typescript</span>&quot;: &quot;<span class="synConstant">^4.9.5</span>&quot;
  <span class="synSpecial">}</span>,
  &quot;<span class="synStatement">description</span>&quot;: &quot;&quot;
<span class="synSpecial">}</span>
</pre>


<p><code>src/</code> 配下に <code>index.ts</code> というファイルを作成します。
<code>tsconfig.json</code> に <code>"rootDir": "./src"</code> と指定しているので、 <code>src/</code> 配下に <code>.ts</code> のコードを配置すれば自動で TypeScript の<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>対象となります。</p>

<ul>
<li><code>src/index.ts</code></li>
</ul>


<pre class="code ts" data-lang="ts" data-unlink>const message: string = &#34;Hello World!!&#34;;

console.log(message);</pre>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>します。</p>

<pre class="code shell" data-lang="shell" data-unlink>% npx tsc </pre>


<p><code>tsconfig.json</code> に <code>""outDir": "./dist"</code> と指定しているので、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>したコードが <code>./dist</code> 配下に生成されます。
<code>index.js</code>, <code>index.js.map</code> というファイルが生成されました。</p>

<pre class="code shell" data-lang="shell" data-unlink>% ls dist/
index.js    index.js.map</pre>


<p>それぞれ中身は下記のようになっています。</p>

<p><code>dist/index.js</code> は<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>によって生成された <a class="keyword" href="http://d.hatena.ne.jp/keyword/JavaScript">JavaScript</a> コードです。</p>

<ul>
<li>dist/index.js</li>
</ul>


<pre class="code js" data-lang="js" data-unlink>&#34;use strict&#34;;
const message = &#34;Hello World!!&#34;;
console.log(message);
//# sourceMappingURL=index.js.map%</pre>


<p><code>.map</code> 拡張子の方はソースマップファイルというものらしく、<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>前と<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%B3%A5%F3%A5%D1%A5%A4%A5%EB">コンパイル</a>後のファイルの対応が記載されたファイルのようです。</p>

<ul>
<li>dist/index.js.map</li>
</ul>


<pre class="code lang-json" data-lang="json" data-unlink><span class="synSpecial">{</span>&quot;<span class="synStatement">version</span>&quot;:<span class="synConstant">3</span>,&quot;<span class="synStatement">file</span>&quot;:&quot;<span class="synConstant">index.js</span>&quot;,&quot;<span class="synStatement">sourceRoot</span>&quot;:&quot;&quot;,&quot;<span class="synStatement">sources</span>&quot;:<span class="synSpecial">[</span>&quot;<span class="synConstant">../src/index.ts</span>&quot;<span class="synSpecial">]</span>,&quot;<span class="synStatement">names</span>&quot;:<span class="synSpecial">[]</span>,&quot;<span class="synStatement">mappings</span>&quot;:&quot;<span class="synConstant">;AAAA,MAAM,OAAO,GAAW,eAAe,CAAC;AAExC,OAAO,CAAC,GAAG,CAAC,OAAO,CAAC,CAAC</span>&quot;<span class="synSpecial">}</span>%
</pre>


<p>Node.js で実行してみます。</p>

<pre class="code shell" data-lang="shell" data-unlink>% node dist/index.js
Hello World!!</pre>


<p>正常に実行できました。</p>

<h2 id="まとめ">まとめ</h2>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/macOS">macOS</a> 上で TypeScript コードを作成してそれを Node.js で動かしてみるところまでの過程をまとめました。
似たようなコマンドがいくつか登場して、慣れないとどれが何をやっているのかわかりませんでしたが、各ツールについてもちょっと理解できました。</p>

<h2 id="参考">参考</h2>

<ul>
<li><a href="https://www.typescriptlang.org/">TypeScript: JavaScript With Syntax For Types.</a></li>
<li><a href="https://github.com/microsoft/TypeScript">GitHub - microsoft/TypeScript: TypeScript is a superset of JavaScript that compiles to clean JavaScript output.</a></li>
<li><a href="https://www.npmjs.com/package/npx">npx - npm</a></li>
<li><a href="https://github.com/hokaccha/nodebrew">GitHub - hokaccha/nodebrew: Node.js version manager</a></li>
<li><a href="https://github.com/nvm-sh/nvm">GitHub - nvm-sh/nvm: Node Version Manager - POSIX-compliant bash script to manage multiple active node.js versions</a></li>
<li><a href="https://docs.volta.sh/guide/">Introduction | Volta</a></li>
<li><a href="https://github.com/yarnpkg/berry">GitHub - yarnpkg/berry: &#x1F4E6;&#x1F408; Active development trunk for Yarn &#x2692;</a></li>
<li><a href="https://github.com/npm/npm/releases/tag/v5.2.0">Release v5.2.0 (2017-07-05) &middot; npm/npm &middot; GitHub</a></li>
<li><a href="https://typescriptbook.jp/reference/tsconfig/tsconfig.json-settings">tsconfig.json&#x3092;&#x8A2D;&#x5B9A;&#x3059;&#x308B; | TypeScript&#x5165;&#x9580;&#x300E;&#x30B5;&#x30D0;&#x30A4;&#x30D0;&#x30EB;TypeScript&#x300F;</a></li>
<li><a href="https://www.typescriptlang.org/docs/handbook/tsconfig-json.html">TypeScript: Documentation - What is a tsconfig.json</a></li>
</ul>


