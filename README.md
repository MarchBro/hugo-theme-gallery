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
├── about.md     <--   не указан в списке альбомов
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
├── bridge.jpg        <-- миниатюра сайта (OpenGraph, etc.)
└── nature/
    ├── index.md      <-- содержит `cover: true` for `tree.jpg`
    ├── nature1.jpg
    ├── nature2.jpg
    └── tree.jpg      <-- миниатюра альбома
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

### Обложка альбома

По умолчанию обложкой альбома является первое изображение в его папке. Чтобы выбрать конкретное изображение (которое должно быть частью альбома), используйте параметр `cover` ниже:

```plain
---
title: Nature
resources:
  - src: tree.jpg
    params:
      cover: true
---
```

Вы можете скрыть изображения из галереи и использовать их только в качестве обложки:

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

Названия изображений для просмотра в лайтбоксе берутся либо из тега `ImageDescription` EXIF tag, либо `title` в метаданных ресурса.

Теги EXIF можно создавать с помощью таких программ, как Adobe Lightroom, или с помощью инструментов командной строки, таких как exiftool:

```sh
exiftool -ImageDescription="Крупный план морды серой кошки" cat-4.jpg
```

В качестве альтернативы, заголовок изображения может быть установлен на обложке

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

Так же доступна произвольная кастомизация:

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

### Категории 

Если вы используете категории в своих альбомах, на главной странице отображается список категорий. Убедитесь, что `term` не включена в `disabledKinds` в конфигурации сайта.

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

Категории также могут иметь собственные заголовки и описания (по умолчанию категория «животные» будет иметь заголовок «Животные» и не будет описания). Просто создайте `content/categories/<category>/_index.md`:

content/categories/animals/\_index.md:

```plain
---
title: Cute Animals
description: This is the description text of the "animals" category.
---
```

#### Список категорий 

Чтобы включить список категорий, у каждой категории должно быть, по крайней мере, изображение в папке `content/categores/<category>/` . Кроме того, `taxonomy`  быть включен в `disableKinds` конфигурации сайта.

Затем, `/categories` отобразится список категорий с изображением их обложки.

#### Другие Таксономии

Вы также можете использовать другие таксономии, например `series`. Обратите внимание, что только  `categories` и `tags` включены в настройках Hugo по умолчанию. Использование `series` в качестве дополнительной таксономии остаётся на усмотрение пользователя.

### Рекомендуемый контент на главной странице

Альбомы (а также страницы таксономии, такие как категории) могут быть отмечены как «избранные»:

```plain
---
title: Featured Album
params:
  featured: true
---
```

При использовании в сочетании с `private: true`  этот альбом отображается только как избранный альбом на главной странице, а не в каком-либо списке альбомов.

Обратите внимание, что категории или любой другой таксономический термин также могут быть отмечены как избранные, поэтому вы можете выделить целую category, series, etc.

По умолчанию на главной странице отображается

- название сайта,
- ссылки на все категории (если категории включены и используются)
- самый последний показанный контент (даже если он приватный)
- все непубличные альбомы высшего уровня

Это можно легко настроить, используя `layouts/_default/home.html`.

### Связанный Контент

Если на вашем сайте доступен связанный контент (например, при использовании ключевых слов или тегов), под каждой галереей отображаются связанные альбомы. Подробнее об этом читайте в [Hugo Docs](https://gohugo.io/content-management/related/#configure-related-content).

Вот пример раздела в `config/_default/hugo.toml` для включения связанного контента:

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

### Иконки соцсетей 

Используйте ключ конфигурации `socialIcons` для добавления значков социальных сетей в нижней части каждой страницы:

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

### Исключить исходные изображения 

Чтобы исключить исходные фотографии из опубликованного сайта и отключить кнопку «Скачать», вы можете использовать параметр конфигурации `build.publishResources` Либо добавьте его в конкретный альбом, либо используйте  `cascade` чтобы исключить исходные изображения из всех под-альбомов. При использовании `publishResources: false` на опубликованный сайт будут включены только изображения с изменённым размером (без каких-либо метаданных), что позволит сэкономить место на диске.

```toml
cascade:
  build:
    publishResources: false
```

### Пользовательский CSS

CSS генерируется с помощью Hugo Pipes, поэтому вы можете добавить дополнительный CSS в `assets/css/custom.css` (см. пример в `exampleSite`).

### Пользовательский JavaScript

Вы можете добавить дополнительный JavaScript в `assets/js/custom.js`.

## Автор

- [Nico Kaiser](https://kaiser.me/)
