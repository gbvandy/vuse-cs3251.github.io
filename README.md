# Adding a New Page

Note: usage of a staging environment is **strongly** encouraged. See below for instructions.

* Create a new file in the root directory with the name `some-new-file.md`.
* Make sure the file starts with YAML front matter:
  ```yaml
  ---
  layout: default
  title: Some New File
  author: John Doe
  ```
* Add an entry in `_config.yml` under `navbar_links` with a `url` and `text`
  attribute to make the link show up in the navbar header:
  * `url` will correspond to the `href` of the link, and should be relative to
    the root of the website (specifically, relative to `site.baseurl`)
  * `text` will correspond to the text of the link that will be created

  ```yaml
  navbar_links: [
    ...,
    {"url": "/href-target", "text": "link text"},
    ...
  ]
  ```

## Staging

It is strongly recommended that you fork this repository and use your fork as a staging environment:

* Fork this repository.
* Configure your fork to be a GitHub Project Page, built off `master`.
* Push changes to *your* fork.
* Pull request into this repository once changes are deployed on your fork and verified good.

Note that you should have an understanding of how `site.baseurl` works when deploying to your fork.


# Running the Website Locally

## Setting Up

Install Ruby and RubyGems; then install `bundler` and dependencies:
~~~
gem install bundler && bundle install --binstubs
~~~

## Development

To build static assets and serve the website:
~~~
./bin/jekyll serve --host=0.0.0.0
~~~

Add the `--watch` and `--incremental` flag to rebuild the website as you modify files (note: you *must* manually rebuild the website if you change `_config.yml`; jekyll does not support automatic reloading on configuration changes):
~~~
./bin/jekyll serve --host=0.0.0.0 --watch
~~~
