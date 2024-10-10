> What does this button do?

Random ramblings about Mac admin stuff and occasional linux or general tech

---

### Latest Post
    
{% assign latest_post = site.posts.first %}

<div class="latest-post">
    <h4><a href="{{ latest_post.url }}">{{ latest_post.title }}</a></h4>
    <sup><em>{{ latest_post.date | date: "%B %d, %Y" }}</em></sup>

    <div>
        {{ latest_post.excerpt | markdownify }}
    </div>

    <small><a href="{{ latest_post.url }}">Read more ...</a></small>
</div>

---