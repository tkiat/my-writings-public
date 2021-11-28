# I Switch from Custom-Built GitHub Pages to Pelican

I did a previous GitHub Pages website from scratch where I designed a blog using simple book carousel animation and also set up back-end (Terraform, CodeCommit, S3, DynamoDB) for blog data connected to GitHub Pages. That was admittedly overkilled for a simple blog but I just wanted to learn AWS. It took more than 2 months with an almost full-time workload. I now prefer using a static site generator and call it a day.

Jekyll has native support on GitHub. I tried it but I had some problems with Ruby on Guix System. Guix is not a conventional distro which means I couldn't just blindly follow Ruby tutorial and made it work. I didn't want to hack and understand Guix just to publish a static website so I used Pelican instead.

Pelican is a static site generator just like Jekyll but it is based on Python. It supports GitHub Pages out of the box. I come across a tutorial on [a fedoramagazine blog](https://fedoramagazine.org/make-github-pages-blog-with-pelican) which worked well for me.

## Configure Pelican

- Highlighting. Pelican utilizes CodeHilite for the Markdown document. It uses the Pygments library to highlight code blocks ([list of available languages](https://pygments.org/languages/)). It sometimes tries to guess the language and encloses what it thinks is an error in a red box. I don't like it so I overwrite the default by setting `guess_lang` to `false`.

```python
# pelicanconf.py
MARKDOWN = {
    'extension_configs': {
        'markdown.extensions.codehilite': {'css_class': 'highlight', 'guess_lang': 'False', 'linenums': 'True'},
        'markdown.extensions.extra': {},
        'markdown.extensions.meta': {},
    },
    'output_format': 'html5',
}
```

- Theme. The most starred theme for Pelican seemed to be Elegant but, in my opinion, it is not minimal enough. I searched through [list of Pelican themes](https://github.com/getpelican/pelican-themes) and finally selected [voce theme](https://github.com/limbenjamin/voce). It looks quite minimal while the navigation is easy thanks to the well-placed tags.

## Configure the voce Theme

- The theme uses Jinja, an HTML template engine. I added the word count of each article by adding `| wordcount`.
- The theme uses Font Awesome version 4.5.0 by default. I need to upgrade to 4.6.0 to use the fa-GitLab icon.
- I made the theme more compact and changed the background color to dark.

## Configure GitHub Pages

- Add .nojekyll at the root directory. A classic source of bugs for many.
