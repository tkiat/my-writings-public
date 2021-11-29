# How to Use CDN with Local JS and CSS Fallbacks

CDN can offer a great cut in delay perceived by a web user by distributing content in many servers across the globe and connects a user to the nearest one. However, the downtime of a CDN server might cost a company a fortune. It's therefore wise to have a fallback on a CDN asset just in case.

## JavaScript

A JavaScript file usually has variable and function declarations, so you check if one, or a few, of those variables, exists. I got the idea below of how to make a fallback script for jQuery from Stack Overflow. It really makes sense to me since 'jQuery' is quite a unique name for a function. Why jQuery in 2021? It comes with my chosen Pelican theme and I am lazy to remove it.

```html
<!--jQuery-->
<script
  src="https://code.jquery.com/jquery-3.5.1.min.js"
  integrity="sha256-9/aliU8dGd2tb6OSsuzixeV4y/faTqgFtohetphbbj0="
  crossorigin="anonymous"
></script>

<!--jQuery fallback-->
<script>
  if (typeof jQuery == 'undefined') {
    console.log('jquery-3.5.1.min.js CDN failed ... fallback to local')
    document.write(
      unescape("%3Cscript src='/extra/jquery-3.5.1.min.js'%3E%3C/script%3E")
    )
  }
</script>
```

## CSS

Font Awesome is a very popular set of icons. The fallback is necessary otherwise a user might be unable to use an icon-based navigation menu. Unlike JavaScript, CSS has no variable declarations. However, you can try your best to check for the unique attribute such as class name in that CSS file and serve the fallback if not found.

```html
<!--font awesome-->
<link
  rel="stylesheet"
  href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css"
/>

<!--font awesome fallback-->
<script>
  window.onload = function () {
    var span = document.createElement('span')
    span.className = 'fa'
    span.style.display = 'none'
    document.body.insertBefore(span, document.body.firstChild)
    if (
      window.getComputedStyle(span, null).getPropertyValue('font-family') !==
      'FontAwesome'
    ) {
      console.log(
        'fail to fetch font-awesome.min.css from CDN ... fallback to local'
      )
      var link = document.createElement('link')
      link.rel = 'stylesheet'
      link.type = 'text/css'
      link.href = '/extra/Font-Awesome-4.7.0/css/font-awesome.min.css'
      document.head.appendChild(link)
    }
    document.body.removeChild(span)
  }
</script>
```
