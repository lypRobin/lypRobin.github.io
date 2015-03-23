---
layout: default
title: Blog Index
---

<div class="col-md-8">
<ul class="list-unstyled">
{% for cat in site.categories %} 
	{% if cat[0] == 'blog' %} 
  		<a name="{{ cat[0] }}"></a>
 		<h1>Technology({{ cat[1].size }})</h1> 
     		{% for post in cat[1] %} 
    			<li><h2><a href="{{ post.url }}">{{ post.title }}</a></h2>{{ post.excerpt }}</li>
		{% endfor %} 
	{% endif %} 
{% endfor %} 
</ul>
</div>
<div class="col-md-4">
	<div class="well">
		<h3>Tags</h3>
		
			{% for tag in site.tags %}
				<li><a href="/tags.html#{{ tag[0] }}">{{ tag[0] }}({{ tag[1].size }})</li>
				
			{% endfor %}

	</div>

</div>
