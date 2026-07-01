# bartreardon.github.io

Source for [Stuff about things](https://bartreardon.github.io/) — a personal blog about Mac systems administration, scripting, and related tools (Munki, Jamf Pro, Outset). Built with [Jekyll](https://jekyllrb.com/) using the `minima` theme, hosted on GitHub Pages.

## Running locally

```
bundle install
bundle exec jekyll serve
```

Then open http://localhost:4000.

## Writing a new post

```
./new "Post Title"
```

This creates a new file under `_posts/` with the date-prefixed filename and front matter already filled in (including a `tags:` field — replace `Other` with real topic tags). Content goes below the `<!--more-->` marker for anything that shouldn't appear in the homepage/RSS excerpt.
