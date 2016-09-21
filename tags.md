---
layout: default
title: tags details
---

<div class="col-md-8">
<ul class="list-unstyled">
{% for tag in site.tags %}
	<li><h2 name="{{ tag[0] }}">{{ tag[0] }}({{ tag[1].size }})</h2>
		<ul>
		{% for post in tag[1] %}
    			<li><h3><a href="{{ post.url }}">{{ post.title }}</a></h3></li>
		{% endfor %}
		</ul>
	</li>			
{% endfor %}

</ul>
</div>
<div class="col-md-4">
	<div class="well">
		<h3>Tags</h3>

			{% for tag in site.tags %}
				<li><a href="/tags.html#{{ tag[0] }}">{{ tag[0] }}({{ tag[1].size }})</a></li>

			{% endfor %}

	</div>

</div>
