---
layout: page
title: Tags
---

<div class="row-fluid">
  <div class="col-xs-12">
    <ul class="list-group" id="tags">
    {% for tag in site.tags %}
      <li class="list-group-item">
        <span class="ord-alph"><b>{{ tag[0] }}</b></span>
        <span class="badge ord-freq">[# of Posts: {{ tag[1].size }}]</span>
        <ul class="list-group hidden">
        {% for post in tag[1] %}
          <li class="list-group-item">
            <span class="label label-info">
              {{ post.date | date_to_string }}
            </span>
            <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
          </li>
        {% endfor %}
        </ul>
      </li>
    {% endfor %}
    </ul>
  </div>
</div>
