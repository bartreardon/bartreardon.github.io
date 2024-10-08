## Adding a JIRA issue collector to Jamf Self Service for user feedback

If your org is one of the many that uses JIRA internally for tracking workflow or projects then you'll know about issue collectors. I've used issue collectors before in munki and my post on that is still available here https://groups.google.com/d/msg/munki-dev/PwvrYaqKxGc/97w7G-USFwUJ

For Jamf Self Service it needs a little bit of extra work as unlike Managed Software Centre there's not a lot of interface modification you can do. That said, it's still fairly simple to set up.

Step 1 - create your issue collector in JIRA - consult your jira docco on how to do that but the simplest is to set "Prominent" trigger style and then pick a template. I went with "Custom" and selected "Description" and "Attach File" custom fields. Add an appropriate trigger text and message.

Step 2 - create a page to add the generated issue collector code to. Here's a template I created earlier:

```
<html>
    
<head>
</head>

<body>
</body>

</html>
```

Save it as `feedback.html` and place it on a web server somewhere.

Step 3 - We aren't done yet (believe it or not). We need to add in our issue collector code. Go grab it from the issue collector setting in JIRA and paste it in between `<body>` and `</body>` tags. All going well, when you re-load your html file you should have a blank page with a JIRA feedback link at the top like this:

![](https://breardon.home.blog/wp-content/uploads/2019/10/image.png?w=361)

That's not super ideal though - we want to see the form straight away. A simple way to do this (because JIRA is weird and don't let you easily just create a blank issue collector page) is to create an onload event and have a snippet of JS click the "Provide Feedback" link for us.

Step 4 - Copy the following into the body of your ever growing html file:

```
<script>  
    window.addEventListener('load', function() {
    document.getElementById('atlwdg-trigger').click(); 
    })
</script>
```

Now if you re-load your page it should pop up the issue collector straight away (bonus points if you have SSO enabled and it picks up who you are straight away - otherwise have a play around with creating an anonymous issue collector):

![](https://breardon.home.blog/wp-content/uploads/2019/10/screen-shot-2019-10-25-at-4.56.22-pm.png?w=931)

Step 5 - Now jump onto your JSS and create a new bookmark and link it to the URL of the feedback html (icon shamelessly ripped off from the Apple Feedback Assistant)

<figure>

![](https://breardon.home.blog/wp-content/uploads/2019/10/image-2.png?w=197)

<figcaption>

  


</figcaption>

</figure>

Boom.

![](https://breardon.home.blog/wp-content/uploads/2019/10/screen-shot-2019-10-25-at-4.59.34-pm.png?w=1024)

Optional - I also created a background image to display on the page so when someone submits their feedback and the form disappears, they see a happy message 😃

![](https://breardon.home.blog/wp-content/uploads/2019/10/image-4.png?w=411)
