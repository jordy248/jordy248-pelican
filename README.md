# jordy248
Pelican that builds jordy248.github.io

jordynelson.io is hosted by [GitHub Pages](https://pages.github.com/) and powered by [Pelican](https:///getpelican.com) using a customized version of [Theme Attila](https://github.com/arulrajnet/attila).

## Publishing
1. Clone both [jordy248](https://github.com/jordy248/jordy248) (which contains the Pelican generator) and [jordy248.github.io](https://github.com/jordy248/jordy248.github.io) (which contains the static site generated by Pelican):
    * `git clone https://github.com/jordy248/jordy248.git`
    * `git clone https://github.com/jordy248/jordy248.github.io.git`
2. In the `jordy248` directory, make any changes to the Pelican configuration file, theme files, or content
    * Compile the Sass:
        * `sass themes/martian/static/css/sass/main.scss themes/martian/static/css/main.css`
3. In the `jordy248` directory, run Pelican to generate the site:
    * `pelican`
        * Optionally: `pelican /path/to/your/content/ [-s path/to/your/settings.py]` if `PATH` (which specifies path to directory containing content to be published) isn't specified in the settings.py/pelicanconf.py file
        * Optionally: `pelican --write-selected output/posts/my-post-title.html` to only [publish certain content](http://docs.getpelican.com/en/stable/settings.html#writing-only-selected-content)
4. The content will be written to `./output` (or wherever `OUTPUT_PATH` specifies in the settings.py/pelicanconf.py file)
5. Preview changes:
    * `cd output`
    * `python -m http.server`
6. Move the contents of `jordy248/output` to `jordy248.github.io` and commit the changes to both `jordy248` and `jordy248.github.io`