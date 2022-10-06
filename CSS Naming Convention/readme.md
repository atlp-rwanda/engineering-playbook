# CSS Naming Convention with [BEM](http://getbem.com/)

CSS without a predefined structure and order can quickly become messy and unmaintainable, for large CSS codebase, this can be a developer's nightmare. In order to introduce some level of structure and order to our CSS, it is important that we have a predefined way we name our CSS styling rules.

## BEM (Block Element Modifier)

Block Element Modifier is a methodology for naming our CSS styles. According to [getbem.com](http://getbem.com/), BEM is a highly useful, powerful, and simple naming convention that makes your front-end code easier to read and understand, easier to work with, easier to scale, more robust and explicit, and a lot more strict.

### B (Block)

A block is a top-level node, the highest level abstraction of a component. It is a standalone entity or a component, on a web page.

**_Example_**

```
.btn {

  rules
}
```

### E (Element)

Elements are children of a block. They have no standalone meaning. And are semantically tied to a block.

**_Example_**

```
.btn__text {

  rules
}

.btn__icon {

  rules
}
```

> Elements are prefixed with their block name and a double underscore.

### M (Modifiers)

Modifiers are used to change the appearance of a block or an element. They provide us a way of creating variations of blocks and elements rules.

**_Example_**

```
.btn--lg {

  rules
}

.btn__text--light {

  rules
}

.btn__icon--right {

  rules
}
```

> Modifiers are prefixed with a block or an element's class name and a double dash.

### Archiving BEM in SASS

The above examples can be archived in a cleaner way by making use of the ampersand(&) in sass.

**_Example_**

```
.btn {

  rules

  &__text {

    rules

    &--light{

      rules
    }
  }

  &__icon {

    rules

    &--right {

      rules
    }
  }

  &--lg {

     rules
  }
}
```

## Further Reading

* [BEM's official website](getbem.com/)

* [CSS Tricks](css-tricks.com/bem-101/)

* [https://cssguidelin.es/#bem-like-naming](https://cssguidelin.es/#bem-like-naming)

* [https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/](https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)
