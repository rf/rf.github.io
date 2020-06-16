---
layout: page
title: Hi, I'm Russ.
---
{% include JB/setup %}

<div style="float:right;text-align:center;display:inline-block;">
<img width=400 height=400 src="assets/me3.jpg" class="me well" />
<br>
<a href="http://twitter.com/#!/russjf" class="zocial icon twitter" style="margin:5px;">twitter</a>
<a href="http://github.com/rf" class="zocial icon github" style="margin:5px;">github</a>
</div>

<p>
I'm a software engineer living in Brooklyn. I also make music, program live 
lights, and cook. I'm currently helping people make things better at 
<a href="http://oden.io/">Oden Technologies</a>.
</p>

<p>
I also play several instruments and produce music with my band, 
<a href="http://snacks.band">Snacks Chapman</a>.
</p>

<div class="clear"></div>

-------

<div id="projects">

  <a class="project" href="projects/rutgers-mobile-app.html">
    <img src="projects/mobile_thumb.png"/>
    Rutgers Mobile App
  </a>

  <a class="project" href="projects/nd.html">
    <img src="projects/nd_thumb.png"/>
    nd
  </a>

  <a class="project" href="projects/hackru.html">
    <img src="projects/hackru_thumb.png"/>
    HackRU
  </a>

  <a class="project" href="projects/snapchan.html">
    <img src="projects/snapchan_thumb.png"/>
    Snapchan
  </a>

  <a class="project" href="projects/renderizer.html">
    <img src="projects/renderizer_thumb.png"/>
    Renderizer
  </a>

  <a class="project" href="projects/hackfsu.html">
    <img src="projects/hackfsu_thumb.png"/>
    HackFSU
  </a>

  <a class="project" href="projects/music.html">
    <img src="projects/music_thumb.png"/>
    Music
  </a>

  <a class="project" href="projects/tsm.html">
    <img src="projects/tsm_thumb.png"/>
    tsm
  </a>

  <a class="project" href="projects/essay-beefer.html">
    <img src="projects/essay_beefer_thumb.png"/>
    Essay Beefer
  </a>

  <a class="project" href="projects/cheapchap.html">
    <img src="projects/cheapchap_thumb.png"/>
    Cheapchap
  </a>

  <a class="project" href="projects/runr.html">
    <img src="projects/runr_thumb.png"/>
    Runr
  </a>

  <a class="project" href="projects/keeno.html">
    <img src="projects/keeno_thumb.png"/>
    Keeno
  </a>

  <a class="project" href="projects/mockti.html">
    <img src="projects/mockti_thumb.png"/>
    MockTi
  </a>

  <a class="project" href="projects/nextbusjs.html">
    <img src="projects/nextbusjs_thumb.png"/>
    NextbusJS 
  </a>

  <a class="project" href="projects/trollnet.html">
    <img src="projects/trollnet_thumb.png"/>
    Trollnet 
  </a>

  <a class="project" href="projects/moar.html">
    <img src="projects/moar_thumb.png"/>
    Moar 
  </a>

</div>

-------

<ul>{% for post in site.posts %}
   <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}</ul>
