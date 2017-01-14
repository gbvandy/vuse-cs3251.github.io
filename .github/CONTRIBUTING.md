# Contributing

You must deploy your changes to a staging environment with GitHub Pages properly configured; the best way to do this is to fork your repository and use the corresponding Projects Page as a staging environment. Directions for doing so can be found below.


## Using a Fork as a Staging Environment

* Fork this repository.
* Configure your fork to be a GitHub Project Page, built off `master`.
* Push changes to *your* fork.
* Pull request into this repository once changes are deployed on your fork and verified good.

# Things to Keep in Mind

## Be Careful with Relative URLs

Note that you should have an understanding of how `site.baseurl` works when deploying to your fork; specifically, any paths relative to the root of your website should be piped through the `relative_url` liquid template tag. In other words, where you might write

```markdown
[link relative to website root](/relative-to-website-root)
```

you should actually write

```markdown
[link relative to website root]({{ "/relative-to-website-root" | relative_url }})
```

