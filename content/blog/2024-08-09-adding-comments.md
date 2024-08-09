---
title: "Adding comments to a static website"
taxonomies:
  tags: ["blog", "website"]
extra:
  comments: true
---

This website is created using [zola](https://www.getzola.org) which is a static website generated. Which means that dynamic user generated content (like comments) is going to be hard. 

But I have recently discovered [giscus](https://giscus.app/). It is a comment system powered by github discussions. And since my website is almost completely run on top of github (pages and actions), it makes sense to keep the comments on my posts inside of it. 

After you run through the configuration wizard on the home page and added the giscus app and settings to your repository, you are presented with a handy script tag that you can add to your page. 

```javascript
<script src="https://giscus.app/client.js"
        data-repo="gamedolphin/gamedolphin.github.com"
        data-repo-id="R_kgDOMgpr2g"
        data-category="Announcements"
        data-category-id="DIC_kwDOMgpr2s4ChgX6"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="top"
        data-theme="noborder_gray"
        data-lang="en"
        data-loading="lazy"
        crossorigin="anonymous"
        async>
</script>
```

## Adding to zola

To add this script to all "pages" (not a page index like the [blog](/blog)), I can extend the theme's `page.html` template by creating a new `templates/page.html`. Zola uses the [tera](https://keats.github.io/tera/docs/) templating system and to add a comments section to the footer of a page, I can extend the `{{footer}}` block.

```handlebars
{% extends "no-style-please/templates/page.html" %}

{% block footer %}
{{ super }}
  {% if page.extra.comments %}
  <script src="from above"></script>
  {% endif %}
{% endblock footer %}
```

Now if I want to add comments to a post, I can add the following meta data to a page, and have a github discussions powered comments section just below it. 

```markdown
---
extra:
  comments: true
---
```

I've added comment sections to a few blog posts already which you can track in this [commit](https://github.com/gamedolphin/gamedolphin.github.com/commit/0f3bb75dd112adec5adcae95e43b6fb979fb1ff7). GG gisqus. If everything works as expected, you would see a comments section just below the tags for this post. You would need to sign in to github before posting ofc. 
