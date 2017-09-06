---
layout: default
title: "分类：Categories"
---


<div id="index" class="row">
	<script type="text/javascript">

function GetQueryString(name)
{
     var reg = new RegExp("(^|&)"+ name +"=([^&]*)(&|$)");
     var r = window.location.search.substr(1).match(reg);
     if(r!=null)return  unescape(r[2]); return null;
}
	// var dataStr = '{ {% for cat in site.categories %}{% if cat[0] != site.categories.first[0] %},{% endif %}"{{ cat[0] }}":[{% for post in cat[1] %}{% if post != cat[1].first %},{% endif %}{"url":"{{post.url}}", "title":"{{post.title}}","desc":"{% if post.excerpt.size > 32 %}{{ post.excerpt }}{% else %}{{ post.content | strip_html | strip_newlines | truncate: 160 }}{% endif %}", "date":"{{post.date | date:"%d/%m/%Y"}}"}{% endfor %}]{% endfor %} }',
 //    data = JSON.parse(dataStr),
 //    curTag = GetQueryString("cat"),
 //    archieves = data[curTag];
</script>
<!-- Loop output paged posts -->
<div id="index" class="row">
    <div class="post-area">
     	 <div class="post-list-body">
        <div post-cate="All">
        <ul class="posts">
	{% for post in paginator.posts %}
	{% if post.category == GetQueryString("cat") %}
	<li>	
	<h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
	<span class="date">{{ post.date | date_to_string }}</span>
	<hr>
	<p>{% if post.excerpt.size > 32 %}{{ post.excerpt }}{% else %}{{ post.content | strip_html | strip_newlines | truncate: 160 }}{% endif %}</p>
		<p> <a href="{{ post.url }}"><span >阅读全文 &raquo; </span></a></p>
	<hr>
	</li>
	{% endfor %}
	{% endif %}
	</ul>
	<!-- Pager indicator -->
	<ul class="pager">
	  {% if paginator.previous_page %} {% if paginator.previous_page == 1 %}
	  <li class="previous">
	    <a href="{{ site.url }}/">
	      &larr; Older
	    </a>
	  </li>
	  {% else %}
	  <li class="previous">
	    <a href="{{ site.url }}/page{{ paginator.previous_page }}">
	      &larr; Older
	    </a>
	  </li>
	  {% endif %} {% endif %} {% if paginator.next_page %}
	  <li class="next">
	    <a href="{{ site.url }}/page{{ paginator.next_page }}">
	      Newer &rarr;
	    </a>
	  </li>
	  {% endif %}
	</ul>
	</div>


	 </div>

</div>
</div>
