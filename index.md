> What does this button do?

Random ramblings about Mac admin stuff and occasional linux or general tech

---

<div class="latest-post">
    {% assign latest_post = site.posts.first %}

    <h3>Latest Post</h3>

    <h4>[{{ latest_post.title }}]({{ latest_post.url }})</h4>

    <sup>_{{ latest_post.date | date: "%B %d, %Y" }}_</sup>

    <div>
        {{ latest_post.excerpt | markdownify }}
    </div>

    [<small>Read more...]({{ latest_post.url }})</small>
</div>

---