# `String.prototype.toCaseFold`: case-insensitive comparison in JavaScript

## Motivation

With Unicode, the correct way to compare two strings in a case-insensitive manner is to compare their case-folded representations. There's a good [writeup](https://www.w3.org/International/wiki/Case_folding) on the W3C website explaining some of the motivation.

One would simply get the wrong answer for some strings through other algorithms such as converting all strings to uppercase, all to lower case, or even upper case and then lower case. Case folding is a locale-sensitive operation. As an illutration of why case folding is a distinct, locale-sensitive operation, in most locales, "i" is case-insensitive unequal to "ı". In English, "i" is case-insensitive equal to "I", but in Turkish, it is case-insensitive equal to "İ" and not "I".

Case folded strings are not very useful for human consumption, but they are useful for comparisons. For example, they can be compared for equality directly, or used as a key in a map. A map with case-folded keys, where lookups are done after case-folding the input, can provide a way to relate case-insensitive strings to values (see the examples section for an implementation). Many other algoriths and standards make reference to case folding; for example, HTML uses [compatibility caseless comparison](https://html.spec.whatwg.org/#case-sensitivity-and-string-comparison) for radio buttons, which is based on case folding and normalization.

JavaScript provides `String.prototype.localeCompare` which superficially sounds related to this topic, but it is in fact a completely different operation. `localeCompare` will return `0` if two strings are the same with respect to collation (e.g., if they normalize to the same thing, or have code points in them with identical collation keys), but it is case-sensitive and simply a distinct Unicode algorithm based on different data tables. `toCaseFold` is needed for the case-insensitive comparison use case.

## Examples

### Comparing two strings for equality in the default locale

```js
function caseInsenstiveEqual(str1, str2) {
  str1.toCaseFold(locale) == str2.toCaseFold(locale)
}
```

### Map of case-insensitive strings to values for a user locale

```js
class CaseInsensitiveMap {
  constructor(locale) {
    this.locale = locale;
    this.map = new Map();
  }
  get(key) {
    return this.map.get(key.toLocaleCaseFold(this.locale));
  }
  set(key, value) {
    this.map.set(key.toLocaleCaseFold(this.locale), value);
  }
}
```

## API

### `String.prototype.toCaseFold()`

Analogous to `String.prototype.toLowerCase`, but looking up the case folded value.

### `String.prototype.toLocaleCaseFold(locale)`

Specified in ECMA 402, analogous to `String.prototype.toLocaleLowerCase`.

## Future work

Unicode specifies other algorithms in this vein which could be exposed to JavaScript, such as
- Title case conversion, another locale-sensitive operation
- Collation keys, which can be used to implement sorting more efficiently than doing binary comparison
