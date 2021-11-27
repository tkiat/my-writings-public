# How I Decouple Articles from My Blog

## Why?

I used to host my blog on GitHub using Pelican. It is an excellent static site generator but it requires metadata syntax on top of each Markdown file.

```yaml
Title: My super title
Date: 2010-12-03 10:20
Modified: 2010-12-05 19:30
Category: Python
Tags: pelican, publishing
Slug: my-super-post
Authors: Alexis Metaireau, Conan Doyle
Summary: Short version for index and feeds

This is the content of my super blog post.
```

<center>Took from https://docs.getpelican.com/en/4.6.0/content.html</center>

This has a reusability issue. What if I want to publish this Markdown also on another website using different technologies? What if I have 500 Markdown files and cannot change them manually? I can at least use `sed` command to remove specific metadata in all articles, but what about I want to add it again in a different format?

## How?

My front-end code first performs asynchronous requests to get list.json from my server. It will generate navigation bars and article menus with texts and links that correspond to list.json. The link is simply in the format `category/subcategory/slug`, and it is valid because this matches the file structure on the server.

I finally find a way to decouple articles from my blog, that is, having a metadata file of any format (I choose JSON, let's name it list.json) that describes the file structure stored in the server. A client at first does an asynchronous request to get that list.json then generates all navigation items with all links based on that list.json.

### Metadata File

```json
[
  {
    "category": "my-category",
    "children": [
      {
        "subcategory": "my-subcategory",
        "children": [
          {
            "date": "XXXX-XX-XX",
            "title": "My Title",
            "slug": "my-article"
          }
        ]
      }
    ]
  }
]
```

<center>list.json</center>

list.json contains an array of category objects. A category object contains an array of subcategory objects. A subcategory object contains an array of metadata for an article. Your structure may be different but I find this structure self-containing and extensible.

### File Structure

On the server, I name an article file according to `slug` field in list.json and put that article inside its subcategory folder, which is inside its category folder. The file structure is laid out in a way that information in list.json is enough to refer to any article.

```text
blog/
|--category1/
|  |--subcategory1/
|  |  |--slug1.md
|  |--subcategory2/
|     |--slug2.md
|     |--slug3.md
|--category2/
|
.
.
```

<center>File Structure on the Server</center>
