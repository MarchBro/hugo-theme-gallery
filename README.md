# Тема Hugo Галерея 

Простая и классная тема hugo

- [Demo](https://nicokaiser.github.io/hugo-theme-gallery/)
- [Example site source](https://github.com/nicokaiser/hugo-theme-gallery/tree/main/exampleSite)

---

![Screenshot](https://github.com/nicokaiser/hugo-theme-gallery/raw/main/images/screenshot.jpg)

---

## Характеристики 

- Адаптивный дизайн
- Темная цветовая гамма (может быть установлена для каждой страницы)
- Личные альбомы
- Выравнивание альбомов по ширине с помощью [Flickr's](https://github.com/flickr/justified-layout)
- Лайтбокс с фотосвайпом [PhotoSwipe](https://photoswipe.com/)
- SEO с тегами Open Graph
- Автоматический (или ручной) выбор художественных изображений / обложек
  
Важное примечание: не пытайтесь использовать изображения WebP. В реализации WebP на Golang, используемой в Hugo, есть ошибка, которая приводит к неправильным уровням изображения (тусклым изображениям) при изменении размера. Подробнее см. [nicokaiser/hugo-theme-gallery#102](https://github.com/nicokaiser/hugo-theme-gallery/issues/102)

## Установка 

Для этой темы требуется Hugo Extended >= 0.123.0. Зависимости включены в комплект, поэтому Node.js/NPM и PostCSS не требуются.

### Как модуль Hugo

Требуется установленный двоичный файл Go.

hugo mod 

```sh 
hugo mod init github.com/<your_user>/<your_project>
```

Затем добавьте тему в свой `hugo.toml`:

```toml
[module]
  [[module.imports]]
    path = "github.com/nicokaiser/hugo-theme-gallery/v4"
```

### Как субмодуль Git

```sh
git submodule add --depth=1 https://github.com/nicokaiser/hugo-theme-gallery.git themes/gallery
```

## Использование

Пакеты страниц, содержащие хотя бы одно изображение, перечислены как альбом или галерея:

```plain
content/
├── _index.md
├── about.md             <--   не указан в списке альбомов
├── animals/
│   ├── _index.md
│   ├── cats/
│   |   ├── index.md
│   |   ├── cat1.jpg
│   |   └── feature.jpg  <-- обложка альбома
│   ├── dogs/
│   |   ├── index.md
│   |   ├── dog1.jpg     <-- обложка альбома
│   |   └── dog2.jpg
│   └── feature.jpg
├── bridge.jpg           <-- миниатюра сайта (OpenGraph, etc.)
└── nature/
    ├── index.md         <-- содержит `cover: true` for `tree.jpg`
    ├── nature1.jpg
    ├── nature2.jpg
    └── tree.jpg         <-- миниатюра альбома
```

- `/about.md` этот пакет страниц не содержит мзображений и не отображается в списке альбомов
- `/nature` этот пакет имеет `index.md` ) => отображается как галерея(`single` макет).
- `/animals` это пакет "ветвь" имеет `_index.md` и дочерние элементы) => отображается в виде списка альбомов (`list` макет).
- В качестве миниатюры для списков альбомов используется ресурс изображения с *feature* в названии или первое найденное изображение.
- Альбомы без изображения не отображаются
  
### Фронт мастер 

- `title` -- название альбома, отображаемое в списке альбомов и на странице альбома.
- `date` -- дата альбома, используемая для сортировки (самая новая первая).
- `description` -- описание, отображаемое на странице альбома. Отображается в формате Markdown, чтобы можно было добавлять ссылки и форматировать текст.
- `weight` -- может использоваться для настройки порядка сортировки.
- `params.featured_image` -- имя файла изображения, используемого для миниатюры альбома. Если не задано, используется первое изображение, содержащее `feature` в имени файла, в противном случае — первое изображение в альбоме. (Устарело, используйте `resources.params.cover`)
- `params.private` -- если установлено значение  `true`, этот альбом не отображается в списке альбомов и исключается из RSS-каналов.
- `params.featured` -- если установлено значение `true`, этот альбом отображается на главной странице (даже если он закрытый). , this album is featured on the homepage (even if private).
- `params.sort_by` --  свойство, используемое для сортировки изображений в альбоме. По умолчанию используется`Name` (filename), в противном слечае`Date`.
- `params.sort_order` -- порядок сортировки. Значение по умолчанию `asc`.
- `params.theme` -- цветовая тема для этой страницы. По умолчанию используется значение `defaultTheme` из конфигурации.  

### Album Cover

By default, the cover image of an album is the first image in its folder. To select a specific image (which must be part of the album), use the `cover` resource parameter in the front matter:

```plain
---
title: Nature
resources:
  - src: tree.jpg
    params:
      cover: true
---
```

You can hide images from the gallery and use them only as cover image:

```plain
---
title: Nature
resources:
  - src: nature-cover.jpg
    params:
      cover: true
      hidden: true
---
```

### Image Metadata

Image titles for the lightbox view are either taken from the `ImageDescription` EXIF tag, or the `title` in the resource metadata.

EXIF tags can be written using software like Adobe Lightroom or by using command line tools like exiftool:

```sh
exiftool -ImageDescription="A closeup of a gray cat's face" cat-4.jpg
```

Alternatively, the image title can be set in the front matter:

```plain
---
date: 2024-02-18T14:12:44+0100
title: Cats
resources:
  - src: cat-1.jpg
    title: Brown tabby cat on white stairs
    params:
      date: 2024-02-18T13:04:30+0100
  - src: cat-4.jpg
    title: A closeup of a gray cat's face
---
```

This also enables custom ordering:

```plain
---
sort_by: Params.weight
resources:
  - src: image-1.jpg
    params:
      weight: 20
  - src: image-2.jpg
    params:
      weight: 10
---
```

### Categories

If you use categories in your albums, the homepage displays a list of categories.
Make sure `term` is not included in `disabledKinds` in the site config.

content/dogs/index.md:

```plain
---
date: 2023-01-12
title: Dogs
categories: ["animals", "nature"]
resources:
  - src: dogs-title-image.jpg
    params:
      cover: true
---
```

Categories can also have custom titles and descriptions (by default, the "animals" category will have "Animals" as title and no description). Just create a `content/categories/<category>/_index.md`:

content/categories/animals/\_index.md:

```plain
---
title: Cute Animals
description: This is the description text of the "animals" category.
---
```

#### List of Categories

To enable a list of categories, each category must at least have an image in the `content/categores/<category>/` folder. Also, `taxonomy` must _not_ be included in the `disableKinds` in the site config.

Then, `/categories` displays a list of categories, with their cover image.

#### Other Taxonomies

You can also use other taxonomies like `series`. Note that only `categories` and `tags` are enabled by Hugo's default settings. Using `series` as additional taxonomy is left as an exercise for the reader.

### Featured Content on the Homepage

Albums (and als taxonomy pages like categories) can be marked as "featured":

```plain
---
title: Featured Album
params:
  featured: true
---
```

When used in combination with `private: true` this album is only shown as featured album on the homepage, and not in any album list.

Note that also categories or any other taxonomy term can be marked as featured, so you can feature a whole category, series, etc.

By default, the homepage displays

- the site title,
- links to all categories (if categories are enabled and used)
- the most recent featured content (even if private)
- all non-private top-level albums

This can easily be adjusted by using a local version of `layouts/_default/home.html`.

### Related Content

If related content is available for your site (e.g. when keywords or tags are used), related albums are shown below each gallery. Read more about this in the [Hugo Docs](https://gohugo.io/content-management/related/#configure-related-content).

Here is an example section in `config/_default/hugo.toml` to enable related content:

```toml
[related]
  includeNewer = true
  threshold = 10
  toLower = false
  [[related.indices]]
    applyFilter = false
    cardinalityThreshold = 0
    name = 'categories'
    pattern = ''
    toLower = false
    type = 'basic'
    weight = 10
  [[related.indices]]
    applyFilter = false
    cardinalityThreshold = 0
    name = 'keywords'
    pattern = ''
    toLower = false
    type = 'basic'
    weight = 50
```

### Social Icons

Use the `socialIcons` configuration key to add social icons on the bottom of each page:

```toml
[params]
  ...
  [params.socialIcons]
    facebook = "https://www.facebook.com/"
    instagram = "https://www.instagram.com/"
    github = "https://github.com/nicokaiser/hugo-theme-gallery/"
    youtube = "https://www.youtube.com/"
    email = "mailto:user@example.com"
    linkedin = "https://linkedin.com/"
```

### Exclude original photos

To exclude the original photos from the published site, and to disable the "Download" button, you can use the `build.publishResources` configuration option. Either add it to a specific album, or use `cascade` to omit the originals from all sub-albums. With `publishResources: false` only the resized images (without any metadata) are included in the published site, which can save quite some disk space.

```toml
cascade:
  build:
    publishResources: false
```

### Custom CSS

CSS is generated with Hugo Pipes, so you can add additional CSS in `assets/css/custom.css` (see example in `exampleSite`).

### Custom JavaScript

You can add additional JavaScript in `assets/js/custom.js`.

## Author

- [Nico Kaiser](https://kaiser.me/)
