---
categories: [Linux]
tags: [nginx,视频,播放器]
---

<p>
    &nbsp;&nbsp;&nbsp;&nbsp;系统:Centos 6.5 x64<br/>
</p>
<p>
    &nbsp;&nbsp;&nbsp;&nbsp;安装nginx<br/>
</p>
<pre class="brush:bash;toolbar:false">yum -y install openssl-devel pcre-devel zlib-devel
useradd www
tar zxvf nginx-1.6.2.tar.gz
cd nginx-1.6.2
./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_flv_module
make &amp;&amp; make install</pre>
<p>
    &nbsp; 编辑nginx.conf 添加
</p>
<pre class="brush:bash;toolbar:false;">location ~\.flv$ {
                flv;
                }</pre>
<p>
    安装处理flv视频的yadmi
</p>
<pre class="brush:bash;toolbar:false">tar xzf yamdi-1.9.tar.gz
cd yamdi-1.9
gcc yamdi.c -o yamdi -O2 -Wall
make install</pre>
<p>
    处理视频添加关键帧
</p>
<pre class="brush:bash;toolbar:false">yamdi -i test.flv -o testy.flv</pre>
<p>
    ckplayer 代码参考
</p>
<pre class="brush:js;toolbar:false">&lt;script type=&quot;text/javascript&quot;&gt;
        var flashvars={
                f:&#39;http://laomm.comupload/testy.flv&#39;,
                c:0,
                b:1,
                h:&#39;4&#39;,//播放http视频流时采用何种拖动方法，=0不使用任意拖动，=1是使用按关键帧，=2是按时间点，=3是自动判断按什么(如果视频格式是.mp4就按关键帧，.flv就按关键时间)，=4也是自动判断(只要包含字符mp4就按mp4来，只要包含字符flv就按flv来)
                q:&#39;&#39;//视频流拖动时参考函数，默认是start
                };
        var params={bgcolor:&#39;#FFF&#39;,allowFullScreen:true,allowScriptAccess:&#39;always&#39;,wmode:&#39;transparent&#39;};
        CKobject.embedSWF(&#39;ckplayer/ckplayer.swf&#39;,&#39;a1&#39;,&#39;ckplayer_a1&#39;,&#39;600&#39;,&#39;400&#39;,flashvars,params);        
&lt;/script&gt;</pre>
<p>
    这样播放视频就可以任意点击哪就可以播放视频了,不需要事先缓冲视频.
</p>
<p>
    附上一个批量用yamdi添加关键帧的脚本
</p>
<pre class="brush:bash;toolbar:false"> #!/bin/bash
 mkdir out
 flv=`ls *.flv`
        for i in $flv
        do
        /usr/local/bin/yamdi -i $i -o flvy
        mv flvy out/$i
        done</pre>
<p>
    <br/>
</p>
