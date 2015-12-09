## Rejected Ideas
### nil
No valid use case.

```scss
// Input ColaSass
@debug nil;

// Output Sass
@debug $cola-nil;

// Imported via ColaSass library
$cola-nil: unquote('');
```

### length
How would you check the length of a list with one item?

```scss
// Input ColaSass
@debug length(s t r i n g);
@debug length('string');

// Output Sass
@debug cola-length(s t r i n g); // 6
@debug cola-length('string');    // 6

// Imported via ColaSass library
@function cola-length($value) {
  // ...
}
```

### nth
How would you get the first item of a list with one item?

```scss
// Input ColaSass
@debug nth('string', 2);
@debug nth(s t r i n g, 2);

// Output Sass
@debug cola-nth('string', 2);    // t
@debug cola-nth(s t r i n g, 2); // t

// Imported via ColaSass library
@function cola-index($value, $argument) {
  // ...
}
```

### index
How would you get the first index of a list with one item?

```scss
// Input ColaSass
@debug index(s t r i n g, 't');
@debug index('string', 't');

// Output Sass
@debug cola-index(s t r i n g, 't'); // 2
@debug cola-index('string', 't');    // 2

// Imported via ColaSass library
@function cola-index($value, $argument) {
  // ...
}
```
