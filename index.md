> What does this button do?

Random ramblings about Mac admin stuff and occasional linux or general tech

---

<div class="latest-post">
    {% assign latest_post = site.posts.first %}

    ### Latest Post

    #### [{{ latest_post.title }}]({{ latest_post.url }})

    <sup>_{{ latest_post.date | date: "%B %d, %Y" }}_</sup>

    {{ latest_post.excerpt | strip_html }}

    [<small>Read more...]({{ latest_post.url }})</small>
</div>

---