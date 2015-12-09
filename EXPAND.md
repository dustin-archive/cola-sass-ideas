# Misc Ideas

## @expand
PostCSS / Gulp Task

Expand converts `@expand .class` to an HTML element rather than concatenating a list of CSS selectors.

Simmilar to Sass's `@extend`.

### Reasoning?
+ CSS is included in the document as one large chunk
+ Extending extends can create very large lists of selectors, which is counter productive for modularity
+ HTML is included as needed and not as one large chunk

### Result?
+ Reduced file-size overall
+ Increased modularity in ColaSass/Sass
+ No messy selector lists are output in the CSS

#### Input
```css
.class1 {
  color: blue;
}

.class2 {
  @expand .class1;
  background-color: orange;
}
```

```html
<div class='.class2'></div>
```

#### Output
```css
.class1 {
  color: blue;
}

.class2 {
  background-color: orange;
}
```

```html
<div class='.class1 .class2'></div>
```
