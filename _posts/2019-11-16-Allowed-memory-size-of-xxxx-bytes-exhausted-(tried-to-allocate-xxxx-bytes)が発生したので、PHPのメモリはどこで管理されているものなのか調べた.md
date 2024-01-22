---
layout: post
Categories:  ["tech"]
Description:  " php-fpmを動かしていて以下のようなエラーが出た時には、memory_limit 以上にメモリを割り当てようとしているためだと思う。  Allowed memory size of xxxx bytes exhausted (trie"
Tags: ["php", "tech"]
date: "2019-11-16T12:04:00+09:00"
title: "Allowed memory size of xxxx bytes exhausted (tried to allocate xxxx bytes)が発生したので、PHPのメモリはどこで管理されているものなのか調べた"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>-fpmを動かしていて以下のようなエラーが出た時には、memory_limit 以上にメモリを割り当てようとしているためだと思う。</p>

<pre class="terminal"> Allowed memory size of xxxx bytes exhausted (tried to allocate xxxx bytes) ...
 </pre>


<p>ここでpsでプロセスのメモリ使用状態を見た時に、<code>php.ini</code>の<code>memory_limit</code>で設定した値以上に<a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>-fpmプロセスがメモリを確保しているように見えるときがある。
例えば、<code>memory_limit</code>を<code>16M</code>にしていた時のプロセスの状態を見ると、メモリは<code>36860(36M)</code>割り当てられているように見えるけど、<a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>-fpmは普通に動いている。
psで見える物理メモリは実際に書き込まれたときに増えるはずなので<a class="keyword" href="http://d.hatena.ne.jp/keyword/%BC%C2%A5%E1%A5%E2%A5%EA">実メモリ</a>に36M程度割り当てられていることになる...はず。</p>

<pre class="terminal"> hoge       28849  0.0  3.4 427360 36860 ?        S    Dec22   2:37  \_ php-fpm: pool example.com
 </pre>


<p>そもそも<a class="keyword" href="http://d.hatena.ne.jp/keyword/php">php</a>-fpmはどのようにメモリを確保しているのか知らない...とか思い始めたので調べてみた。</p>
</body>

<!-- more -->

<body>
<h3>
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>のメモリ管理の仕組みを見る</h3>


<p>該当のエラーが発生した時のエラーメッセージを元に調べると、<a target="_brank" rel="noopener noreferrer" herf="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c">zend_alloc.c</a> の<code>zend_mm_alloc_pages()</code>, <code>zend_mm_alloc_huge()</code>, <code>zend_mm_realloc_huge()</code> の中で定義されている模様。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c#L1462"><code>zend_alloc.c</code>の<code>zend_mm_alloc_pages()</code>の中のエラー処理</a></li>
</ul>
<pre class="terminal"> #if ZEND_MM_LIMIT
                if (UNEXPECTED(ZEND_MM_CHUNK_SIZE &gt; heap-&gt;limit - heap-&gt;real_size)) {
                    if (zend_mm_gc(heap)) {
                        goto get_chunk;
                    } else if (heap-&gt;overflow == 0) {
#if ZEND_DEBUG
                        zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted at %s:%d (tried to allocate %zu bytes)", heap-&gt;limit, __zend_filename, __zend_lineno, size);
#else
                        zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted (tried to allocate %zu bytes)", heap-&gt;limit, ZEND_MM_PAGE_SIZE * pages_count);
#endif
                        return NULL;
                    }
                }
 </pre>





<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c#L1462"><code>zend_alloc.c</code>の<code>zend_mm_realloc_huge()</code>の中のエラー処理</a></li>
</ul>
<pre class="terminal"> #if ZEND_MM_LIMIT
            if (UNEXPECTED(new_size - old_size &gt; heap-&gt;limit - heap-&gt;real_size)) {
                if (zend_mm_gc(heap) &amp;&amp; new_size - old_size &lt;= heap-&gt;limit - heap-&gt;real_size) {
                    /* pass */
                } else if (heap-&gt;overflow == 0) {
#if ZEND_DEBUG
                    zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted at %s:%d (tried to allocate %zu bytes)", heap-&gt;limit, __zend_filename, __zend_lineno, size);
#else
                    zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted (tried to allocate %zu bytes)", heap-&gt;limit, size);
#endif
                    return NULL;
                }
            }
 </pre>





<ul>
  <li>
<a target="_brank" rel="noopener noreferrer" href="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c#L1788"><code>zend_alloc.c</code>の<code>zend_mm_alloc_huge()</code></a>の中のエラー処理</li>
</ul>
<pre class="terminal"> #if ZEND_MM_LIMIT
    if (UNEXPECTED(new_size &gt; heap-&gt;limit - heap-&gt;real_size)) {
        if (zend_mm_gc(heap) &amp;&amp; new_size &lt;= heap-&gt;limit - heap-&gt;real_size) {
            /* pass */
        } else if (heap-&gt;overflow == 0) {
#if ZEND_DEBUG
            zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted at %s:%d (tried to allocate %zu bytes)", heap-&gt;limit, __zend_filename, __zend_lineno, size);
#else
            zend_mm_safe_error(heap, "Allowed memory size of %zu bytes exhausted (tried to allocate %zu bytes)", heap-&gt;limit, size);
#endif
            return NULL;
        }
    }
#endif
 </pre>



<p>エラーが発生したら<code>zend_mm_safe_error()</code>が呼び出されるようだった。</p>

<p>いずれの関数も <code>limit(memory_limit)</code>と今割り当てられているページサイズ<code>(real_size)</code>の差を計算し、その差より割り当てようとしているメモリが多い場合は<a class="keyword" href="http://d.hatena.ne.jp/keyword/GC">GC</a>(<code>zend_mm_gc()</code>)を実行し、それでもあふれる場合はエラーとなる...ように見える。
<code>zend_mm_gc()</code>の中身は見ていない。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c#L364"><code>zend_alloc.c</code>の<code>zend_mm_safe_error()</code></a></li>
</ul>
<pre class="terminal"> static ZEND_COLD ZEND_NORETURN void zend_mm_safe_error(zend_mm_heap *heap,
    const char *format,
    size_t limit,
#if ZEND_DEBUG
    const char *filename,
    uint32_t lineno,
#endif
    size_t size)
{

    heap-&gt;overflow = 1;
    zend_try {
        zend_error_noreturn(E_ERROR,
            format,
            limit,
#if ZEND_DEBUG
            filename,
            lineno,
#endif
            size);
    } zend_catch {
    }  zend_end_try();
    heap-&gt;overflow = 0;
    zend_bailout();
    exit(1);
}
 </pre>



<p>で、これらのエラーの時のメモリはどの値を見ているのかと思い、各関数を見ていると、<code>zend_mm_heap</code>の値を参照しているようだった。</p>

<ul>
  <li><a target="_brank" rel="noopener noreferrer" href="https://github.com/php/php-src/blob/PHP-7.3.13/Zend/zend_alloc.c#L228"><code>zend_alloc.c</code>の<code>_zend_mm_heap()</code>の中のエラー処理</a></li>
</ul>
<pre class="terminal"> struct _zend_mm_heap {
#if ZEND_MM_CUSTOM
    int                use_custom_heap;
#endif
#if ZEND_MM_STORAGE
    zend_mm_storage   *storage;
#endif
#if ZEND_MM_STAT
    size_t             size;                    /* current memory usage */
    size_t             peak;                    /* peak memory usage */
#endif
    zend_mm_free_slot *free_slot[ZEND_MM_BINS]; /* free lists for small sizes */
#if ZEND_MM_STAT || ZEND_MM_LIMIT
    size_t             real_size;               /* current size of allocated pages */
#endif
#if ZEND_MM_STAT
    size_t             real_peak;               /* peak size of allocated pages */
#endif
#if ZEND_MM_LIMIT
    size_t             limit;                   /* memory limit */
    int                overflow;                /* memory overflow flag */
#endif
 </pre>



<p>ここで確保したメモリを管理しているように見える。</p>

<p><code>zend_mm</code>って何の略なんだと思って調べたら Zend Memory Manager のことらしい。
Zend Memory Manager は<code>emalloc()</code>という関数によってメモリを確保している。</p>

<p><code>USE_ZEND_ALLOC=0</code>を指定すると<code>emalloc()</code>を使わず、システムのデフォルトのメモリアロケーターを使うらしい。(<a class="keyword" href="http://d.hatena.ne.jp/keyword/Linux">Linux</a>だと<code>malloc()</code>ってことか？)</p>

<p>ということで、<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>のメモリ管理には独自のもの(Zend Memory Manager)が使われている、ということが分かった。</p>

<p><code>_zend_mm_heap</code>構造体を見ると、<code>memory_limit</code>の値はZend Memory Managerの中で管理されているメモリ量から管理されていることも分かった。</p>

<h4>まとめ</h4>


<p>この記事では<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>のメモリ管理の仕組みについて調べてみて自分なりにわかったことをまとめた。
<a class="keyword" href="http://d.hatena.ne.jp/keyword/PHP">PHP</a>はデフォルトではZend Memory Managerという独自のメモリ管理機能を使っており、<code>emalloc()</code>という関数でメモリを確保していることがわかった。
この場合、<code>memory_limit</code>はZend Memory Managerの中の<code>zend_alloc.c</code>の中の<code>_zend_mm_heap</code>構造体の中で管理されているメモリの状況に応じてエラーハンドリングを行っているらしいことがわかった。</p>

<h4>参考</h4>




<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://wiki.php.net/internals/zend_mm">PHP: internals:zend_mm</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://tekitoh-memdhoi.info/views/766">Allowed memory size ofというエラーはどういうエラーなのか - PHP Zend Engineのメモリ管理</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://nopipi.hatenablog.com/entry/2017/11/11/213214">プロセスのVSZ,RSSとfree,meminfoを実機で確認 - のぴぴのメモ</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://stackoverflow.com/questions/2290611/tracking-memory-usage-in-php">Tracking Memory Usage in PHP</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://matstani.github.io/blog/2013/06/19/php-memory-usage/">[PHP]memory_get_usageのreal_usageオプションについて - /home/matstani/weblog</a></li>
    <li><a target="_brank" rel="noopener noreferrer" href="https://www.php.net/manual/ja/internals2.memory.management.php">&gt;PHP: 基本的なメモリ管理 - Manual</a></li>
</ul>

</body>
