
<p>Scrapy &#x662F;&#x4E00;&#x4E2A;&#x901A;&#x7528;&#x7684;&#x722C;&#x866B;&#x6846;&#x67B6;&#xFF0C;&#x4F46;&#x662F;&#x4E0D;&#x652F;&#x6301;&#x5206;&#x5E03;&#x5F0F;&#xFF0C;Scrapy-redis&#x662F;&#x4E3A;&#x4E86;&#x66F4;&#x65B9;&#x4FBF;&#x5730;&#x5B9E;&#x73B0;Scrapy&#x5206;&#x5E03;&#x5F0F;&#x722C;&#x53D6;&#xFF0C;&#x800C;&#x63D0;&#x4F9B;&#x4E86;&#x4E00;&#x4E9B;&#x4EE5;redis&#x4E3A;&#x57FA;&#x7840;&#x7684;&#x7EC4;&#x4EF6;(&#x4EC5;&#x6709;&#x7EC4;&#x4EF6;)&#x3002;</p>
<blockquote>
<p>pip install scrapy-redis</p>
</blockquote>
<p>Scrapy-redis&#x63D0;&#x4F9B;&#x4E86;&#x4E0B;&#x9762;&#x56DB;&#x79CD;&#x7EC4;&#x4EF6;&#xFF08;components&#xFF09;&#xFF1A;(&#x56DB;&#x79CD;&#x7EC4;&#x4EF6;&#x610F;&#x5473;&#x7740;&#x8FD9;&#x56DB;&#x4E2A;&#x6A21;&#x5757;&#x90FD;&#x8981;&#x505A;&#x76F8;&#x5E94;&#x7684;&#x4FEE;&#x6539;)</p>
<ul>
<li><code>Scheduler</code> </li>
<li><code>Duplication Filter</code></li>
<li><code>Item Pipeline</code></li>
<li><code>Base Spider</code></li>
</ul>
<h2 id="scrapyredis&#x67B6;&#x6784;">scrapy-redis&#x67B6;&#x6784;</h2>
<p><img src="../images/scrapy-redis.png" alt=""></p>
<p>&#x5982;&#x4E0A;&#x56FE;&#x6240;&#x2F70;&#x793A;&#xFF0C;scrapy-redis&#x5728;scrapy&#x7684;&#x67B6;&#x6784;&#x4E0A;&#x589E;&#x52A0;&#x4E86;redis&#xFF0C;&#x57FA;&#x4E8E;redis&#x7684;&#x7279;&#x6027;&#x62D3;&#x5C55;&#x4E86;&#x5982;&#x4E0B;&#x7EC4;&#x4EF6;&#xFF1A;</p>
<h4 id="scheduler&#xFF1A;"><code>Scheduler</code>&#xFF1A;</h4>
<p>Scrapy&#x6539;&#x9020;&#x4E86;python&#x672C;&#x6765;&#x7684;collection.deque(&#x53CC;&#x5411;&#x961F;&#x5217;)&#x5F62;&#x6210;&#x4E86;&#x81EA;&#x5DF1;&#x7684;Scrapy queue(<a href="https://github.com/scrapy/queuelib/blob/master/queuelib/queue.py" target="_blank">https://github.com/scrapy/queuelib/blob/master/queuelib/queue.py)</a>)&#xFF0C;&#x4F46;&#x662F;Scrapy&#x591A;&#x4E2A;spider&#x4E0D;&#x80FD;&#x5171;&#x4EAB;&#x5F85;&#x722C;&#x53D6;&#x961F;&#x5217;Scrapy queue&#xFF0C; &#x5373;Scrapy&#x672C;&#x8EAB;&#x4E0D;&#x652F;&#x6301;&#x722C;&#x866B;&#x5206;&#x5E03;&#x5F0F;&#xFF0C;scrapy-redis &#x7684;&#x89E3;&#x51B3;&#x662F;&#x628A;&#x8FD9;&#x4E2A;Scrapy queue&#x6362;&#x6210;redis&#x6570;&#x636E;&#x5E93;&#xFF08;&#x4E5F;&#x662F;&#x6307;redis&#x961F;&#x5217;&#xFF09;&#xFF0C;&#x4ECE;&#x540C;&#x4E00;&#x4E2A;redis-server&#x5B58;&#x653E;&#x8981;&#x722C;&#x53D6;&#x7684;request&#xFF0C;&#x4FBF;&#x80FD;&#x8BA9;&#x591A;&#x4E2A;spider&#x53BB;&#x540C;&#x4E00;&#x4E2A;&#x6570;&#x636E;&#x5E93;&#x91CC;&#x8BFB;&#x53D6;&#x3002;</p>
<p>Scrapy&#x4E2D;&#x8DDF;&#x201C;&#x5F85;&#x722C;&#x961F;&#x5217;&#x201D;&#x76F4;&#x63A5;&#x76F8;&#x5173;&#x7684;&#x5C31;&#x662F;&#x8C03;&#x5EA6;&#x5668;<code>Scheduler</code>&#xFF0C;&#x5B83;&#x8D1F;&#x8D23;&#x5BF9;&#x65B0;&#x7684;request&#x8FDB;&#x884C;&#x5165;&#x5217;&#x64CD;&#x4F5C;&#xFF08;&#x52A0;&#x5165;Scrapy queue&#xFF09;&#xFF0C;&#x53D6;&#x51FA;&#x4E0B;&#x4E00;&#x4E2A;&#x8981;&#x722C;&#x53D6;&#x7684;request&#xFF08;&#x4ECE;Scrapy queue&#x4E2D;&#x53D6;&#x51FA;&#xFF09;&#x7B49;&#x64CD;&#x4F5C;&#x3002;&#x5B83;&#x628A;&#x5F85;&#x722C;&#x961F;&#x5217;&#x6309;&#x7167;&#x4F18;&#x5148;&#x7EA7;&#x5EFA;&#x7ACB;&#x4E86;&#x4E00;&#x4E2A;&#x5B57;&#x5178;&#x7ED3;&#x6784;&#xFF0C;&#x6BD4;&#x5982;&#xFF1A;</p>
<pre><code>    {
        &#x4F18;&#x5148;&#x7EA7;0 : &#x961F;&#x5217;0
        &#x4F18;&#x5148;&#x7EA7;1 : &#x961F;&#x5217;1
        &#x4F18;&#x5148;&#x7EA7;2 : &#x961F;&#x5217;2
    }
</code></pre><p>&#x7136;&#x540E;&#x6839;&#x636E;request&#x4E2D;&#x7684;&#x4F18;&#x5148;&#x7EA7;&#xFF0C;&#x6765;&#x51B3;&#x5B9A;&#x8BE5;&#x5165;&#x54EA;&#x4E2A;&#x961F;&#x5217;&#xFF0C;&#x51FA;&#x5217;&#x65F6;&#x5219;&#x6309;&#x4F18;&#x5148;&#x7EA7;&#x8F83;&#x5C0F;&#x7684;&#x4F18;&#x5148;&#x51FA;&#x5217;&#x3002;&#x4E3A;&#x4E86;&#x7BA1;&#x7406;&#x8FD9;&#x4E2A;&#x6BD4;&#x8F83;&#x9AD8;&#x7EA7;&#x7684;&#x961F;&#x5217;&#x5B57;&#x5178;&#xFF0C;Scheduler&#x9700;&#x8981;&#x63D0;&#x4F9B;&#x4E00;&#x7CFB;&#x5217;&#x7684;&#x65B9;&#x6CD5;&#x3002;&#x4F46;&#x662F;&#x539F;&#x6765;&#x7684;Scheduler&#x5DF2;&#x7ECF;&#x65E0;&#x6CD5;&#x4F7F;&#x7528;&#xFF0C;&#x6240;&#x4EE5;&#x4F7F;&#x7528;Scrapy-redis&#x7684;scheduler&#x7EC4;&#x4EF6;&#x3002;</p>
<h4 id="duplication-filter"><code>Duplication Filter</code></h4>
<p>Scrapy&#x4E2D;&#x7528;&#x96C6;&#x5408;&#x5B9E;&#x73B0;&#x8FD9;&#x4E2A;request&#x53BB;&#x91CD;&#x529F;&#x80FD;&#xFF0C;Scrapy&#x4E2D;&#x628A;&#x5DF2;&#x7ECF;&#x53D1;&#x9001;&#x7684;request&#x6307;&#x7EB9;&#x653E;&#x5165;&#x5230;&#x4E00;&#x4E2A;&#x96C6;&#x5408;&#x4E2D;&#xFF0C;&#x628A;&#x4E0B;&#x4E00;&#x4E2A;request&#x7684;&#x6307;&#x7EB9;&#x62FF;&#x5230;&#x96C6;&#x5408;&#x4E2D;&#x6BD4;&#x5BF9;&#xFF0C;&#x5982;&#x679C;&#x8BE5;&#x6307;&#x7EB9;&#x5B58;&#x5728;&#x4E8E;&#x96C6;&#x5408;&#x4E2D;&#xFF0C;&#x8BF4;&#x660E;&#x8FD9;&#x4E2A;request&#x53D1;&#x9001;&#x8FC7;&#x4E86;&#xFF0C;&#x5982;&#x679C;&#x6CA1;&#x6709;&#x5219;&#x7EE7;&#x7EED;&#x64CD;&#x4F5C;&#x3002;&#x8FD9;&#x4E2A;&#x6838;&#x5FC3;&#x7684;&#x5224;&#x91CD;&#x529F;&#x80FD;&#x662F;&#x8FD9;&#x6837;&#x5B9E;&#x73B0;&#x7684;&#xFF1A;</p>
<pre><code class="lang-python">    <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">request_seen</span><span class="hljs-params">(self, request)</span>:</span>
        <span class="hljs-comment"># self.request_figerprints&#x5C31;&#x662F;&#x4E00;&#x4E2A;&#x6307;&#x7EB9;&#x96C6;&#x5408;  </span>
        fp = self.request_fingerprint(request)

        <span class="hljs-comment"># &#x8FD9;&#x5C31;&#x662F;&#x5224;&#x91CD;&#x7684;&#x6838;&#x5FC3;&#x64CD;&#x4F5C;  </span>
        <span class="hljs-keyword">if</span> fp <span class="hljs-keyword">in</span> self.fingerprints:
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">True</span>
        self.fingerprints.add(fp)
        <span class="hljs-keyword">if</span> self.file:
            self.file.write(fp + os.linesep)
</code></pre>
<p>&#x5728;scrapy-redis&#x4E2D;&#x53BB;&#x91CD;&#x662F;&#x7531;<code>Duplication Filter</code>&#x7EC4;&#x4EF6;&#x6765;&#x5B9E;&#x73B0;&#x7684;&#xFF0C;&#x5B83;&#x901A;&#x8FC7;redis&#x7684;set &#x4E0D;&#x91CD;&#x590D;&#x7684;&#x7279;&#x6027;&#xFF0C;&#x5DE7;&#x5999;&#x7684;&#x5B9E;&#x73B0;&#x4E86;Duplication Filter&#x53BB;&#x91CD;&#x3002;scrapy-redis&#x8C03;&#x5EA6;&#x5668;&#x4ECE;&#x5F15;&#x64CE;&#x63A5;&#x53D7;request&#xFF0C;&#x5C06;request&#x7684;&#x6307;&#x7EB9;&#x5B58;&#x2F0A;redis&#x7684;set&#x68C0;&#x67E5;&#x662F;&#x5426;&#x91CD;&#x590D;&#xFF0C;&#x5E76;&#x5C06;&#x4E0D;&#x91CD;&#x590D;&#x7684;request push&#x5199;&#x2F0A;redis&#x7684; request queue&#x3002;</p>
<p>&#x5F15;&#x64CE;&#x8BF7;&#x6C42;request(Spider&#x53D1;&#x51FA;&#x7684;&#xFF09;&#x65F6;&#xFF0C;&#x8C03;&#x5EA6;&#x5668;&#x4ECE;redis&#x7684;request queue&#x961F;&#x5217;&#x2FA5;&#x91CC;&#x6839;&#x636E;&#x4F18;&#x5148;&#x7EA7;pop &#x51FA;&#x2F00;&#x4E2A;request &#x8FD4;&#x56DE;&#x7ED9;&#x5F15;&#x64CE;&#xFF0C;&#x5F15;&#x64CE;&#x5C06;&#x6B64;request&#x53D1;&#x7ED9;spider&#x5904;&#x7406;&#x3002;</p>
<h4 id="item-pipeline&#xFF1A;"><code>Item Pipeline</code>&#xFF1A;</h4>
<p>&#x5F15;&#x64CE;&#x5C06;(Spider&#x8FD4;&#x56DE;&#x7684;)&#x722C;&#x53D6;&#x5230;&#x7684;Item&#x7ED9;Item Pipeline&#xFF0C;scrapy-redis &#x7684;Item Pipeline&#x5C06;&#x722C;&#x53D6;&#x5230;&#x7684; Item &#x5B58;&#x2F0A;redis&#x7684; items queue&#x3002;</p>
<p>&#x4FEE;&#x6539;&#x8FC7;<code>Item Pipeline</code>&#x53EF;&#x4EE5;&#x5F88;&#x65B9;&#x4FBF;&#x7684;&#x6839;&#x636E; key &#x4ECE; items queue &#x63D0;&#x53D6;item&#xFF0C;&#x4ECE;&#x2F7D;&#x5B9E;&#x73B0; <code>items processes</code>&#x96C6;&#x7FA4;&#x3002;</p>
<h4 id="base-spider"><code>Base Spider</code></h4>
<p>&#x4E0D;&#x5728;&#x4F7F;&#x7528;scrapy&#x539F;&#x6709;&#x7684;Spider&#x7C7B;&#xFF0C;&#x91CD;&#x5199;&#x7684;<code>RedisSpider</code>&#x7EE7;&#x627F;&#x4E86;Spider&#x548C;RedisMixin&#x8FD9;&#x4E24;&#x4E2A;&#x7C7B;&#xFF0C;RedisMixin&#x662F;&#x7528;&#x6765;&#x4ECE;redis&#x8BFB;&#x53D6;url&#x7684;&#x7C7B;&#x3002;</p>
<p>&#x5F53;&#x6211;&#x4EEC;&#x751F;&#x6210;&#x4E00;&#x4E2A;Spider&#x7EE7;&#x627F;RedisSpider&#x65F6;&#xFF0C;&#x8C03;&#x7528;setup_redis&#x51FD;&#x6570;&#xFF0C;&#x8FD9;&#x4E2A;&#x51FD;&#x6570;&#x4F1A;&#x53BB;&#x8FDE;&#x63A5;redis&#x6570;&#x636E;&#x5E93;&#xFF0C;&#x7136;&#x540E;&#x4F1A;&#x8BBE;&#x7F6E;signals(&#x4FE1;&#x53F7;)&#xFF1A;</p>
<ul>
<li><p>&#x4E00;&#x4E2A;&#x662F;&#x5F53;spider&#x7A7A;&#x95F2;&#x65F6;&#x5019;&#x7684;signal&#xFF0C;&#x4F1A;&#x8C03;&#x7528;spider_idle&#x51FD;&#x6570;&#xFF0C;&#x8FD9;&#x4E2A;&#x51FD;&#x6570;&#x8C03;&#x7528;<code>schedule_next_request</code>&#x51FD;&#x6570;&#xFF0C;&#x4FDD;&#x8BC1;spider&#x662F;&#x4E00;&#x76F4;&#x6D3B;&#x7740;&#x7684;&#x72B6;&#x6001;&#xFF0C;&#x5E76;&#x4E14;&#x629B;&#x51FA;DontCloseSpider&#x5F02;&#x5E38;&#x3002;</p>
</li>
<li><p>&#x4E00;&#x4E2A;&#x662F;&#x5F53;&#x6293;&#x5230;&#x4E00;&#x4E2A;item&#x65F6;&#x7684;signal&#xFF0C;&#x4F1A;&#x8C03;&#x7528;item_scraped&#x51FD;&#x6570;&#xFF0C;&#x8FD9;&#x4E2A;&#x51FD;&#x6570;&#x4F1A;&#x8C03;&#x7528;<code>schedule_next_request</code>&#x51FD;&#x6570;&#xFF0C;&#x83B7;&#x53D6;&#x4E0B;&#x4E00;&#x4E2A;request&#x3002;</p>
</li>
</ul>
                    
     
