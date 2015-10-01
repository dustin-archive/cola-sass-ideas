# Pre-Sass / ColaSass / Ideas

ColaSass compiles to vanilla Sass, so all of your existing Sass code is compatible.

Cola will have a dependency of vanilla Sass functions and mixins used to modify the behavior of Sass.

For example `str-length()` and `length()` will become `length` and compile to `cola-length()`.

The `cola-length()` function will check for a string or list and apply whichever is needed.

## Why?

I love Sass and the community it has around it, but I want a customized syntax and I want people who don't want to use my syntax to still be able to use my stuff.

## Lists

Easier syntax for retrieving items from a list.

```scss
// Input
@debug $list(6);

// Output
@debug nth($list, 6);
```

Easier syntax plus added functionality of assigning/replacing a value in a list. If an index is specified that's longer than the list, the items in-between are set to null;

```scss
// Input ColaSass
$list: 1 2 3;
$list(6): 12;
@debug $list; // Output: 1 2 3 null null 12

// Output Sass
$list: 1 2 3;
$list: cola-list($list, 6, 12);
@debug $list; // Output: 1 2 3 null null 12

// Imported via ColaSass library
@function cola-list($list, $index, $value) {
  // ...
}
```

## Maps

```scss
// Input
@debug $map.key;
@debug $map('key');


// Output
@debug map-get($map, key);
@debug map-get($map, 'key');

@function cola-map($map, $key, $value) {
  // ...
}
```

## Loops

```scss
// Input ColaSass
@each $item, $i in $list {
  // ...
}

// Output Sass
@for $i from 1 through length($list) {
  $item: nth($list, $i);

  // ...
}
```

## is Operator

```scss
// input
@if 1 is 2 {
  // ...
}

// output
@if 1 == 2 {
  // ...
}
```

## null
`type-of(null)` returns `null`, not a string `'null'`.

```scss
// Input ColaSass
@debug type-of(null) == null; // false
@debug type-of(null) == 'null'; // true

// Output Sass
@debug cola-type-of(null) == null; // true
@debug cola-type-of(null) == 'null'; // false

// Imported via ColaSass library
@function cola-type-of($value) {
  @if $value == null {
    @return null;
  }
  @return type-of($value);
}
```

## Properties

```scss
red($variable)        =>  $variable.red
green($variable)      =>  $variable.green
blue($variable)       =>  $variable.blue

hue($variable)        =>  $variable.hue
saturation($variable) =>  $variable.saturation
lightness($variable)  =>  $variable.lightness

unquote($variable)    =>  $variable.unquote
quote($variable)      =>  $variable.quote

str-length($variable) => $variable.length
length($variable)     => $variable.length

etc...
```

## Lazy Evaluation
```scss
//
```

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
