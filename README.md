# H1 Get WiFi Passwords With Python
*This script searches windows for wifi passwords with python already known and displays them alongside the network name. It will not find passwords that your computer doesn't already know. This is useful for the occasions that you forget your WiFi password.*
---
- Quick Background Idea
- Getting the Passwords
- Final Script

<h2 id="quick-background-idea">
<span style="position:relative"><a class="anchor" href="#quick-background-idea"><svg aria-hidden="true" focusable="false" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z" fill-rule="evenodd"></path></svg></a>Quick Background Idea</span></h2>
<p>If you type <code>netsh wlan show profiles</code> in cmd, you will be shown the profiles for wifi connections your computer has stored.</p>
<p>If you then type <code>netsh wlan show profile {Profile Name} key=clear</code>, the output provided will contain the network key which is the WiFi password.</p>
<h2 id="getting-the-passwords">
<span style="position:relative"><a class="anchor" href="#getting-the-passwords"><svg aria-hidden="true" focusable="false" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z" fill-rule="evenodd"></path></svg></a>Getting the Passwords</span></h2>
<p>First import subprocess, this is the module we will use to interact with the cmd.</p>
<div class="python codehilite"><pre><span></span><code><span class="kn">import</span> <span class="nn">subprocess</span>
</code></pre></div>

<p>Next, get the output for the command "netsh wlan show profiles" using subprocess.check_output(). Then decode the output with utf-8 and split the string by a newline character to get each line in a separate string. </p>
<div class="python codehilite"><pre><span></span><code><span class="n">data</span> <span class="o">=</span> <span class="n">subprocess</span><span class="o">.</span><span class="n">check_output</span><span class="p">([</span><span class="s1">&#39;netsh&#39;</span><span class="p">,</span> <span class="s1">&#39;wlan&#39;</span><span class="p">,</span> <span class="s1">&#39;show&#39;</span><span class="p">,</span> <span class="s1">&#39;profiles&#39;</span><span class="p">])</span><span class="o">.</span><span class="n">decode</span><span class="p">(</span><span class="s1">&#39;utf-8&#39;</span><span class="p">)</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">)</span>
</code></pre></div>

<p>Now that we have a list of strings, we can get lines that only contain "All User Profile". With these lines we then need to split it by a ':', get the right hand side and remove the first and last character</p>
<div class="python codehilite"><pre><span></span><code><span class="n">profiles</span> <span class="o">=</span> <span class="p">[</span><span class="n">i</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s2">&quot;:&quot;</span><span class="p">)[</span><span class="mi">1</span><span class="p">][</span><span class="mi">1</span><span class="p">:</span><span class="o">-</span><span class="mi">1</span><span class="p">]</span> <span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="n">data</span> <span class="k">if</span> <span class="s2">&quot;All User Profile&quot;</span> <span class="ow">in</span> <span class="n">i</span><span class="p">]</span>
</code></pre></div>

<p>Now that the variable a contains the WiFi profile names, we can get the output for the command "netsh wlan show profile {Profile Name} key=clear" using subprocess.check_output() again for a particular profile while looping through all profiles.</p>
<div class="python codehilite"><pre><span></span><code><span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="n">profiles</span><span class="p">:</span>
    <span class="n">results</span> <span class="o">=</span> <span class="n">subprocess</span><span class="o">.</span><span class="n">check_output</span><span class="p">([</span><span class="s1">&#39;netsh&#39;</span><span class="p">,</span> <span class="s1">&#39;wlan&#39;</span><span class="p">,</span> <span class="s1">&#39;show&#39;</span><span class="p">,</span> <span class="s1">&#39;profile&#39;</span><span class="p">,</span> <span class="n">i</span><span class="p">,</span> <span class="s1">&#39;key=clear&#39;</span><span class="p">])</span><span class="o">.</span><span class="n">decode</span><span class="p">(</span><span class="s1">&#39;utf-8&#39;</span><span class="p">)</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">)</span>
</code></pre></div>

<p>Still in the loop, find lines that contain "Key Content", split by ':' and remove the first and last character just like before</p>
<div class="python codehilite"><pre><span></span><code>    <span class="n">results</span> <span class="o">=</span> <span class="p">[</span><span class="n">b</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s2">&quot;:&quot;</span><span class="p">)[</span><span class="mi">1</span><span class="p">][</span><span class="mi">1</span><span class="p">:</span><span class="o">-</span><span class="mi">1</span><span class="p">]</span> <span class="k">for</span> <span class="n">b</span> <span class="ow">in</span> <span class="n">results</span> <span class="k">if</span> <span class="s2">&quot;Key Content&quot;</span> <span class="ow">in</span> <span class="n">b</span><span class="p">]</span>
</code></pre></div>

<p>Now we should have a list containing one string which is the particular profiles key. Here you could just use a simple print statement but I have just formatted it a bit.</p>
<div class="python codehilite"><pre><span></span><code>    <span class="k">try</span><span class="p">:</span>
        <span class="nb">print</span> <span class="p">(</span><span class="s2">&quot;</span><span class="si">{:&lt;30}</span><span class="s2">|  </span><span class="si">{:&lt;}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">i</span><span class="p">,</span> <span class="n">results</span><span class="p">[</span><span class="mi">0</span><span class="p">]))</span>
    <span class="k">except</span> <span class="ne">IndexError</span><span class="p">:</span>
        <span class="nb">print</span> <span class="p">(</span><span class="s2">&quot;</span><span class="si">{:&lt;30}</span><span class="s2">|  </span><span class="si">{:&lt;}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">i</span><span class="p">,</span> <span class="s2">&quot;&quot;</span><span class="p">))</span>
</code></pre></div>

<p>Now put an input call at the end of the script outside the loop so that when the script is run it will not immediately stop when results are displayed.</p>
<div class="python codehilite"><pre><span></span><code><span class="nb">input</span><span class="p">(</span><span class="s2">&quot;&quot;</span><span class="p">)</span>
</code></pre></div>

<p>Save this file with a .py extension and you can now run the script. You can run it by double-clicking on the script, running it in IDLE or even cmd using <code>python {filename}</code>.</p>
<h2 id="final-script">
<span style="position:relative"><a class="anchor" href="#final-script"><svg aria-hidden="true" focusable="false" height="16" version="1.1" viewBox="0 0 16 16" width="16"><path d="M4 9h1v1H4c-1.5 0-3-1.69-3-3.5S2.55 3 4 3h4c1.45 0 3 1.69 3 3.5 0 1.41-.91 2.72-2 3.25V8.59c.58-.45 1-1.27 1-2.09C10 5.22 8.98 4 8 4H4c-.98 0-2 1.22-2 2.5S3 9 4 9zm9-3h-1v1h1c1 0 2 1.22 2 2.5S13.98 12 13 12H9c-.98 0-2-1.22-2-2.5 0-.83.42-1.64 1-2.09V6.25c-1.09.53-2 1.84-2 3.25C6 11.31 7.55 13 9 13h4c1.45 0 3-1.69 3-3.5S14.5 6 13 6z" fill-rule="evenodd"></path></svg></a>Final Script</span></h2>
<div class="python codehilite"><pre><span></span><code><span class="kn">import</span> <span class="nn">subprocess</span>

<span class="n">data</span> <span class="o">=</span> <span class="n">subprocess</span><span class="o">.</span><span class="n">check_output</span><span class="p">([</span><span class="s1">&#39;netsh&#39;</span><span class="p">,</span> <span class="s1">&#39;wlan&#39;</span><span class="p">,</span> <span class="s1">&#39;show&#39;</span><span class="p">,</span> <span class="s1">&#39;profiles&#39;</span><span class="p">])</span><span class="o">.</span><span class="n">decode</span><span class="p">(</span><span class="s1">&#39;utf-8&#39;</span><span class="p">)</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">)</span>
<span class="n">profiles</span> <span class="o">=</span> <span class="p">[</span><span class="n">i</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s2">&quot;:&quot;</span><span class="p">)[</span><span class="mi">1</span><span class="p">][</span><span class="mi">1</span><span class="p">:</span><span class="o">-</span><span class="mi">1</span><span class="p">]</span> <span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="n">data</span> <span class="k">if</span> <span class="s2">&quot;All User Profile&quot;</span> <span class="ow">in</span> <span class="n">i</span><span class="p">]</span>
<span class="k">for</span> <span class="n">i</span> <span class="ow">in</span> <span class="n">profiles</span><span class="p">:</span>
    <span class="n">results</span> <span class="o">=</span> <span class="n">subprocess</span><span class="o">.</span><span class="n">check_output</span><span class="p">([</span><span class="s1">&#39;netsh&#39;</span><span class="p">,</span> <span class="s1">&#39;wlan&#39;</span><span class="p">,</span> <span class="s1">&#39;show&#39;</span><span class="p">,</span> <span class="s1">&#39;profile&#39;</span><span class="p">,</span> <span class="n">i</span><span class="p">,</span> <span class="s1">&#39;key=clear&#39;</span><span class="p">])</span><span class="o">.</span><span class="n">decode</span><span class="p">(</span><span class="s1">&#39;utf-8&#39;</span><span class="p">)</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s1">&#39;</span><span class="se">\n</span><span class="s1">&#39;</span><span class="p">)</span>
    <span class="n">results</span> <span class="o">=</span> <span class="p">[</span><span class="n">b</span><span class="o">.</span><span class="n">split</span><span class="p">(</span><span class="s2">&quot;:&quot;</span><span class="p">)[</span><span class="mi">1</span><span class="p">][</span><span class="mi">1</span><span class="p">:</span><span class="o">-</span><span class="mi">1</span><span class="p">]</span> <span class="k">for</span> <span class="n">b</span> <span class="ow">in</span> <span class="n">results</span> <span class="k">if</span> <span class="s2">&quot;Key Content&quot;</span> <span class="ow">in</span> <span class="n">b</span><span class="p">]</span>
    <span class="k">try</span><span class="p">:</span>
        <span class="nb">print</span> <span class="p">(</span><span class="s2">&quot;</span><span class="si">{:&lt;30}</span><span class="s2">|  </span><span class="si">{:&lt;}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">i</span><span class="p">,</span> <span class="n">results</span><span class="p">[</span><span class="mi">0</span><span class="p">]))</span>
    <span class="k">except</span> <span class="ne">IndexError</span><span class="p">:</span>
        <span class="nb">print</span> <span class="p">(</span><span class="s2">&quot;</span><span class="si">{:&lt;30}</span><span class="s2">|  </span><span class="si">{:&lt;}</span><span class="s2">&quot;</span><span class="o">.</span><span class="n">format</span><span class="p">(</span><span class="n">i</span><span class="p">,</span> <span class="s2">&quot;&quot;</span><span class="p">))</span>
<span class="nb">input</span><span class="p">(</span><span class="s2">&quot;&quot;</span><span class="p">)</span>
</code></pre></div>
