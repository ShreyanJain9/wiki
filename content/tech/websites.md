# Websites

## css block vs inline directions

## mobile-first is the default
because of how the css block model works, you have to do literally zero work to get a "mobile" layout. and by that, i mean a one-column layout where 

## split your page into sections

you are probably at the very least familiar with how a basic html document is structured:

```html
<html>
  <head></head>
  <body></body>
</html>
```
let's focus on the body part

you should have your top-level blocks in here -- site header, site footer, maybe site nav if it's not nested in the header... and most importantly main. optionally an aside if you have secondary info related to the main content but separate from it.

```html
<body>
  <header id="site-header"></header>
  <main id="main"></main>
  <footer id="site-footer"></footer>
</body>
```

## what goes inside a section

you probably want a div container to enforce maxwidth of the site.

```html
<section class="section">
  <div class="container">
    <header>
      <h2>Section title goes here</h2>
    </header>
  </div>
</section>
```

### sections vs containers

sections should be used for vertical padding and margins, while containers should be used for horizontal padding and margins:

```css
.section {margin: 1em 0}
.container {margin: 0 auto}
```

### why .section class instead of section element selector?

disambiguation. a "section" may not literally be a `<section>`, it may be an article `<header>` or a `<footer>` or whatever top-level element is immediately wrapping your container. 