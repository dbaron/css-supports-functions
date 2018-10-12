# Explainer: New functions for CSS `@supports` rules

## Background

CSS Level 1 and Level 2 expected CSS authors to deal with unimplemented features through fallback rules: CSS attempted to define rules for what was ignored when unknown (i.e., future) features were encoutered, so that CSS authors could reliably use features that were implemented in only some of the implementations they were targeting.  This allowed overriding a widely-supported feature with a less-supported feature, but it didn't allow more complex types of conditioning of CSS on the presence of browser support.  [CSS Conditional Rules Level 3](https://drafts.csswg.org/css-conditional/) introduced the [`@supports` rule](https://drafts.csswg.org/css-conditional/#at-supports), which allows chunks of CSS to be conditioned on support for particular property:value pairs, such as

```css
@supports ( display: flex ) {
  /* These rules only apply when the "flex" value
     of the "display" property is supported. */
  body, #navigation, #content { display: flex; }
  #navigation { background: blue; color: white; }
  #article { background: white; color: black; }
}
```

This specification is now widely adopted.

One of its deficiencies is that it only allows testing support for CSS features that are properties or values, and not for other parts of CSS, such as selectors and @-rules.

## Selector syntax

Having feature queries based on selectors is probably the highest priority addition, both because it seems the most likely to be important in the near future, and because it is the simplest.  It has come up recently because CSS authors using the features in the [CSS Scrollbars Module Level 1](https://drafts.csswg.org/css-scrollbars-1/) may want to query for support of those features, and may also want to query for support of the `::-webkit-scrollbar` pseudo-element.

I believe a possible syntax for such a feature was discussed at one point early in the development of `@supports`, but I can't find meeting minutes of the discussion.  I think the best way to extend the syntax is using a `selector()` function, as in:

```css
@supports (scrollbar-width: thin) or selector(::-webkit-scrollbar) {
  #chatwindow { overflow: scroll; scrollbar-width: thin }
  #chatwindow::-webkit-scrollbar { width: 5px }
}
```

It is worth noting that this usage requires that the `selector()` feature query function *not* copy the quirks that are present in selector parsing, where all `::-webkit-*` pseudo-elements behave as though they're supported (and allow the selector to parse).  Instead, inside of the `selector()` feature query function, this quirk should not be present, and a `selector()` function containing a `::-webkit-*` pseudo-element should only be true if the pseudo-element is *actually* supported.

Other than this, the function should work just as the tests for support/validity work in the [rules on handling invalid selectors](https://drafts.csswg.org/selectors-4/#invalid).

## `@font-family` descriptors

See [#2463 Feature detection for descriptors](https://github.com/w3c/csswg-drafts/issues/2463).

## CSS Rules

* does putting rules inside `@supports` conditions cause grammar problems?  readability problems?
* how useful is it?

## Issues

* [#3207 feature query function for selectors](https://github.com/w3c/csswg-drafts/issues/3207)
* [#2463 feature query function for descriptors](https://github.com/w3c/csswg-drafts/issues/2463)
* feature query function for rules
