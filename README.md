# My blog

Available on https://hu.szabo.jp

## Custom mods

### Gallery

Adding `gallery: true` to the header of a page/post will insert a gallery to the end with photos from `/assets/{page or post name}`:

```
---
layout: page
title: About
gallery: true
---
```

### Lightbox for images

Adding `#lb` to the end of an image URL will make it a clickable image that opens in a lightbox:

`![Vending machines in rural Tochigi](/assets/2021-05-13-coke-on/vendingmachines.jpeg#lb)`

## Local Development

```
docker run --rm -v $(pwd):/srv/jekyll -p 4000:4000 -it jekyll/jekyll:builder jekyll serve --livereload --future
```

# Lanyon

Lanyon is an unassuming [Jekyll](http://jekyllrb.com) theme that places content first by tucking away navigation in a hidden drawer. It's based on [Poole](http://getpoole.com), the Jekyll butler.

![Lanyon](https://f.cloud.github.com/assets/98681/1825266/be03f014-71b0-11e3-9539-876e61530e24.png)
![Lanyon with open sidebar](https://f.cloud.github.com/assets/98681/1825267/be04a914-71b0-11e3-966f-8afe9894c729.png)


## Contents

- [Usage](#usage)
- [Options](#options)
  - [Sidebar menu](#sidebar-menu)
  - [Themes](#themes)
  - [Reverse layout](#reverse-layout)
- [Development](#development)
- [Author](#author)
- [License](#license)


## Usage

Lanyon is a theme built on top of [Poole](https://github.com/poole/poole), which provides a fully furnished Jekyll setup—just download and start the Jekyll server. See [the Poole usage guidelines](https://github.com/poole/poole#usage) for how to install and use Jekyll.


## Options

Lanyon includes some customizable options, typically applied via classes on the `<body>` element.


### Sidebar menu

Create a list of nav links in the sidebar by assigning each Jekyll page the correct layout in the page's [front-matter](http://jekyllrb.com/docs/frontmatter/).

```
---
layout: page
title: About
---
```

**Why require a specific layout?** Jekyll will return *all* pages, including the `atom.xml`, and with an alphabetical sort order. To ensure the first link is *Home*, we exclude the `index.html` page from this list by specifying the `page` layout.


### Themes

Lanyon ships with eight optional themes based on the [base16 color scheme](https://github.com/chriskempson/base16). Apply a theme to change the color scheme (mostly applies to sidebar and links).

![Lanyon with red theme](https://f.cloud.github.com/assets/98681/1825270/be065110-71b0-11e3-9ed8-9b8de753a4af.png)
![Lanyon with red theme and open sidebar](https://f.cloud.github.com/assets/98681/1825269/be05ec20-71b0-11e3-91ea-a9138ef07186.png)

There are eight themes available at this time.

![Available theme classes](https://f.cloud.github.com/assets/98681/1817044/e5b0ec06-6f68-11e3-83d7-acd1942797a1.png)

To use a theme, add any one of the available theme classes to the `<body>` element in the `default.html` layout, like so:

```html
<body class="theme-base-08">
  ...
</body>
```

To create your own theme, look to the Themes section of [included CSS file](https://github.com/poole/lanyon/blob/master/public/css/lanyon.css). Copy any existing theme (they're only a few lines of CSS), rename it, and change the provided colors.


### Reverse layout

![Lanyon with reverse layout](https://f.cloud.github.com/assets/98681/1825265/be03f2e4-71b0-11e3-89f1-360705524495.png)
![Lanyon with reverse layout and open sidebar](https://f.cloud.github.com/assets/98681/1825268/be056174-71b0-11e3-88c8-5055bca4307f.png)

Reverse the page orientation with a single class.

```html
<body class="layout-reverse">
  ...
</body>
```


### Sidebar overlay instead of push

Make the sidebar overlap the viewport content with a single class:

```html
<body class="sidebar-overlay">
  ...
</body>
```

This will keep the content stationary and slide in the sidebar over the side content. It also adds a `box-shadow` based outline to the toggle for contrast against backgrounds, as well as a `box-shadow` on the sidebar for depth.

It's also available for a reversed layout when you add both classes:

```html
<body class="layout-reverse sidebar-overlay">
  ...
</body>
```

### Sidebar open on page load

Show an open sidebar on page load by modifying the `<input>` tag within the `sidebar.html` layout to add the `checked` boolean attribute:

```html
<input type="checkbox" class="sidebar-checkbox" id="sidebar-checkbox" checked>
```

Using Liquid you can also conditionally show the sidebar open on a per-page basis. For example, here's how you could have it open on the homepage only:

```html
<input type="checkbox" class="sidebar-checkbox" id="sidebar-checkbox" {% if page.title =="Home" %}checked{% endif %}>
```

## Development

Lanyon has two branches, but only one is used for active development.

- `master` for development.  **All pull requests should be to submitted against `master`.**
- `gh-pages` for our hosted site, which includes our analytics tracking code. **Please avoid using this branch.**


## Author

**Mark Otto**
- <https://github.com/mdo>
- <https://twitter.com/mdo>


## License

Open sourced under the [MIT license](LICENSE.md).

<3

## Migrating from WordPress

So this blog was originally posted on WOrdpress, where I had to migrate old posts over.

First I followed this guide to get the posts and Wordpress-hosted images over: https://www.deadlyfingers.net/code/migrating-from-wordpress-to-github-pages I kept the posts html, and didn't convert them to md.

Second I had a lot of photos sourced from Google Photos public albums (one album per post) added to the post using [this addon](https://wordpress.org/plugins/embed-google-photos-album-easily/). Getting them over was a bit tricky and I ended up using the following script:

```
cd /srv/_posts/
for file in $(find -type f -name '*.html'); do
  echo "> Working with $file"; 
  if (grep -Po '\[embed-google-photos-album link="([^"]*)"[^\]]*\]' $file); then
    # Add gallery
    sed -i 's/status: publish/status: publish\ngallery: true/' $file
    link=$(grep -Po '\[embed-google-photos-album link="([^"]*)"[^\]]*\]' $file | grep -Po 'https://photos.app.goo.gl/[^"]+')
    echo "Google album link $link"
    mkdir /srv/assets/$file
    var=1
    for photo in $(/root/abc/node_modules/scrape-google-photos/index.js $link | grep -Po "https://lh3.googleusercontent.com[^']+"); do
      echo $photo
      wget -O /srv/assets/$file/$var.jpg $photo=w1920-h1080
      var=$((var + 1))
    done
  fi
done
```

First of all it was running in a docker container:

```
docker run --rm -v $(pwd):/srv -u 0:0 -it wernight/phantomjs /bin/bash
```

Then I had to install https://github.com/lefuturiste/google-photos-album-crawler like this:
```
apt update
apt install npm -y
cd /root
mdkir abc
cd abc
npm install scrape-google-photos
```

Then run the script which added the `gallery: true` tag to the posts, grabbed all images from the albums and placed them into separate folders under `assets/` named the same as the posts.

Somehow this resulted in a lot of doublicate photos, so I had to remove them ([code from SO](https://superuser.com/a/386209/768525)):

```
declare -A arr
shopt -s globstar
arr=()

for file in **; do
  [[ -f "$file" ]] || continue
   
  read cksm _ < <(md5sum "$file")
  if ((arr[$cksm]++)); then 
    rm $file
  fi
done
```

Then I had to fix embedded YouTube videos, as they ended up being something like:

```
<p><!-- wp:embed {"url":"https://www.youtube.com/watch?v=U0CL-ZSuCrQ","type":"video","providerNameSlug":"youtube","responsive":true,"className":"wp-embed-aspect-16-9 wp-has-aspect-ratio"} --></p>
<figure class="wp-block-embed is-type-video is-provider-youtube wp-block-embed-youtube wp-embed-aspect-16-9 wp-has-aspect-ratio">
<div class="wp-block-embed__wrapper">
https://www.youtube.com/watch?v=U0CL-ZSuCrQ
</div>
</figure>
<p><!-- /wp:embed --></p>
```

Sometimes the YouTube URL was like https://youtu.be/Dd7FeNkoVjI, which had to be handled too. So I did a global search and replace (with VS Code's built-in tool) from 

```
<div class="wp-block-embed__wrapper">\n(?:https://www.youtube.com/watch\?v=(\S+)|https://youtu.be/(\S+))\n</div>
```
 
to

```
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/$1$2" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
```

Next was all the links between posts, which were all absolute (like https://japan.szabo-simon.hu/2021/05/04/biwaichi-biciklivel-a-biwa-to-korul/) and as I was updating the domain, I had to update these too. I decided to use this opportunity to also make them relative (e.g. /2021/05/04/biwaichi-biciklivel-a-biwa-to-korul/) with this regex search and replace:

```
https://japan.szabo-simon.hu([^"]+/)"
```

to

```
$1"
```
