> What does this button do?

Random ramblings about Mac admin stuff and occasional linux or general tech

---

{% assign latest_post = site.posts.first %}

## Latest Post

### [{{ latest_post.title }}]({{ latest_post.url }})
_{{ latest_post.date | date: "%B %d, %Y" }}_

{{ latest_post.excerpt | strip_html | truncatewords: 100 }}

[Read more...]({{ latest_post.url }})

---