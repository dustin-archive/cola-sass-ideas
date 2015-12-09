#  ColaSass - Ideas
ColaSass will compile to vanilla Sass, so all of your existing Sass code is compatible.

ColaSass will use PostCSS for the syntax modifications and a vanilla Sass library for the behavioral modifications.

## Vanilla functions
Prefix any function with `sass` to get vanilla sass functionality.

```scss
// Input ColaSass
@debug sass-type-of(solid); // string

// Output Sass
@debug type-of(solid); // style
```

## Quick example
Complexity is dramatically reduced.

```scss
// Input ColaSass
$results(index): $results(index).join($i, comma);

// Output Sass
$results: map-merge($results, (index: join(map-get($results, index), $i, comma)));
```

## Syntax changes
### iteration
```scss
// Input ColaSass
@debug $i++;
@debug $i--;

// Output Sass
@debug $i: $i + 1;
@debug $i: $i - 1;
```

### is and isnt
```scss
// Input ColaSass
@debug 1 is 2;
@debug 1 isnt 2;

// Output Sass
@debug 1 == 2;
@debug 1 != 2;
```

### Loops
```scss
// Input ColaSass
@for $i from 1 through $half {
  // ...
}

// Output Sass
@if floor($variable) == $variable { // Check for floating points.
  @for $i from 1 through $variable {
    // ...
  }
}
```

```scss
// Input ColaSass
@each $item $i in $list {
  // ...
}

// Output Sass
$cola-through: length($list); // Store functions used in the loop in variables.
@if floor($cola-through) == $cola-through { // Check for floating points.
  @for $i from 1 through $cola-through {
    $item: nth($list, $i);

    // ...
  }
}
```

### Lazy Evaluation
```scss
// Input ColaSass
@function resolve($$expression) {
  @return $$expression;
}

@function convert($value) {
  @return resolve($value / 16);
}

// Output Sass
@function resolve($cola-expression, $value) {
  @return call($cola-expression, $value);
}

@function convert($value) {
  @return resolve('cola-resolve-expression', $value);
}

@function cola-resolve-expression($value) {
  @return $value / 16;
}
```

#### Lazy Evaluation with comments
```scss
// Input ColaSass
// The expression argument here triggers all calls to this function to have
// this argument extracted into an expression.
@function resolve($$expression) {
  // Any time and expression `$$` is used outside an argument, the expression
  // is evaluated.
  @return $$expression;
}

@function convert($value) {
  // Since the function being called uses an expression for the first argument,
  // `$value / 16` will be extracted into an expression.
  // All variables in the expression are passed as arguments for the call.
  @return resolve($value / 16);
}

// Output Sass
@function resolve($cola-expression, $value) {
  @return call($cola-expression, $value);
}

@function convert($value) {
  @return resolve('cola-resolve-expression', $value);
}

// The expression function is prepended with `cola` to show that it's owned by
// ColaSass and appended with `expression` to show that it's an expression.
@function cola-resolve-expression($value) {
  @return $value / 16;
}
```

### Chain-able functions
No more messy nested functions.

```scss
// Input ColaSass
$one.join: $two;
$one.append: $two;
$one.merge: $two;
$one.zip: $two;
$one.call: $two;

// Output Sass
$one: join($one, $two);
$one: append($one, $two);
$one: map-merge($one, $two);
$one: zip($one, $two);
$one: call($one, $two);

// More
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

## Syntax modifications (Require ColaSass Library)
### Lists
#### List get value
```scss
//
// + Input ColaSass
// =============================================================================

$list-1: 1 2 3 null null 12;
$list-2: 1 2 3 null null 12 null 12 null 12;
$list-3: 1 2 3 null null (null (null 12));

.test {
  content: $list-1(6);
  content: $list-2(6 8 10);
  content: $list-3(6:2:2);
}

//
// + Output Sass
// =============================================================================

.test {
  content: cola-list-get($list-1, 6);
  content: cola-list-get($list-2, 6 8 10);
  content: cola-list-get-nested($list-3, 6 2 2);
}

//
// + Output CSS
// =============================================================================

.test {
  content: 12;       // 12
  content: 12 12 12; // 12 12 12;
  content: 12;       // 12;
}
```

#### List set value
```scss
//
// + Input ColaSass
// =============================================================================

$list-1: 1 2 3;
$list-2: 1 2 3;
$list-3: 1 2 3;

.test {
  $list-1(6): 12;
  $list-2(6 8 10): 12;
  $list-3(6:2:2): 12;
  content: $list-1;
  content: $list-2;
  content: $list-3;
}

//
// + Output Sass
// =============================================================================

$list-1: 1 2 3;
$list-2: 1 2 3;
$list-3: 1 2 3;

.test {
  $list-1: cola-list-set($list-1, 6, 12);
  $list-2: cola-list-set($list-2, 6 8 10, 12);
  $list-3: cola-list-set-nested($list-3, 6 2 2, 12);
  content: $list-1;
  content: $list-2;
  content: $list-3;
}

//
// + Output CSS
// =============================================================================

.test {
  content: 1 2 3 12;       // 1 2 3 null null 12
  content: 1 2 3 12 12 12; // 1 2 3 null null 12 null 12 null 12;
  content: 1 2 3 12;       // 1 2 3 null null (null (null 12));
}
```

### Maps
```scss
// Input ColaSass
@debug $map(key);
@debug $map(key): value;
@debug $lists.longest.(index); // largest function returns a map

// Output Sass
@debug map-get($map, key);
@debug map-merge(map, (key:value));
@debug map-get(largest($list), index);

// Imported via ColaSass Library
@function cola-map($map, $key, $value) {
  // ...
}
```

## Function modifications (Require ColaSass Library)
### type-of
```scss
// Input ColaSass
@debug type-of(solid)      == style;
@debug type-of(em)         == length;
@debug type-of(!important) == flag;
@debug type-of(null)       == null;
@debug type-of(border)     == string;
@debug type-of(1)          == number;

// Output Sass
@debug cola-type-of(solid)      == style;  // true
@debug cola-type-of(em)         == length; // true
@debug cola-type-of(!important) == flag;   // true
@debug cola-type-of(null)       == null;   // true
@debug cola-type-of(border)     == string; // true
@debug cola-type-of(1)          == number; // true

// Imported via ColaSass library
@function cola-type-of($value) {
  // ...
}
```

### join
```scss
// Input ColaSass

// Output Sass

// Imported via ColaSass library
@function cola-join($value) {
  // ...
}
```
