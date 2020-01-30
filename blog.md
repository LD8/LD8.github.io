---
layout: page
title: Blog
permalink: /blog/
---

<div class="home">
    <ul class="post-list">
      {%- for post in site.posts -%}
      <li>
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            <small class="post-meta">{{ post.date | date: date_format }} >> </small>{{ post.title | escape }}
          </a>
        </h3>
      </li>
      {%- endfor -%}
    </ul>

    <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>

</div>
