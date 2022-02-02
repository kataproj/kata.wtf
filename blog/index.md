---
layout: page
permalink: /blog
title: Blog
description: "Kata &mdash; features, releases, and tutorials!"
---

Check out all blog posts: [/blog/all]({{ site.baseurl }}/blog/all)

<div class="album">
  <div class="container">
    <div class="row row-cols-1 row-cols-sm-2 row-cols-md-3 g-3">
      {% for post in site.posts %}
        <div class="col">
          {% include card-post.html post=post %}
        </div>
      {% endfor %}
    </div>
  </div>
</div>
