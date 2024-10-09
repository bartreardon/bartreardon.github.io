## Migrating from wordpress to github

🎉 **First Post**

At least a first post hosted on github.io (for as long as the [.io TLD still exists](https://www.computerworld.com/article/3552692/is-the-io-top-level-domain-headed-for-extinction.html))

Every other post on this site was migrated over from wordpress. Issues [over there](https://www.theverge.com/2024/10/4/24262410/wordpress-fight-trademarks-open-source-mullenweg#) as well as the fact that I created this github.io 3 years ago with the intention of using it make for a good time to do this.

### Step 1 - Exporting everything from wordpress

This is relatively easy. 

Log on to wordpress, Tools -> Export and then select "Export All"

<img src="/images/wp1.png" width=300px>

<img src="/images/wp2.png" width=300px>

You will then get a download of your site as an XML file called `yoursitename.wordpress.2024-10-08.000.xml` or something to that effect

### Step 2 - Converting everything to markdown

This is also relatively easy (everything here was run under macOS 15)

 - Install [brew](https://brew.sh)
 - run `brew install node.js`
 - rename your wordpress xml to `export.xml` and put it in a handy folder somewhere
 - cd to the folder where your `export.xml` file is and run `npx wordpress-export-to-markdown --post-folders=false --prefix-date=true`
   - follow the bouncing ball. this will create a folder called `Output` and all your posts and images will be downloaded and converted to markdown format

### Step 3 - Moving it all to github.io

This one will take some finessing. I followed Chad Baldwin's post [here](https://chadbaldwin.net/2021/03/14/how-to-build-a-sql-blog.html) which if you follow will create a `https://yourname.github.io` site using the `minima` [jekyll theme](https://github.com/jekyll/minima)

Clone your repo locally and copy over your generated markdown files and images to the `_posts` directory.

From there you can modify the theme, or if you're handy about it, use another theme. I'll probably tinker with this one over time but for now it's pretty much Minima with some css overides to add background and modify some other elements. Developer mode in whatever browser you use (I use Safari) is super handy for tweaking rather than trying to edit css, publish, wait for github actions to generate the pages, realise you missed something, rinse and repeat. 

### The Future

This makes adding a new post as easy as creating a new file in `YYYY-MM-DD-post-title.md` format and then editing as I see fit. When I'm ready I can commit and push the changes when I'm ready. 

I also have to go over the older posts and do some minor edits as not all the images are displaying but that's something I'll use as inspiration to make future posts and perhaps fill in the blanks from the past year or so to add some "new old stock" posts about things that have happened the last time I posted something.

Happy Blogging 🙂
