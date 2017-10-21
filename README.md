# Tempest CSS Code Standards

Welcome to the CSS standards for Tempest. This document outlines our best practices and philosophies when it comes to writing CSS at Say Media. We follow three guiding principles to produce code that is modular and easy to maintain.

1. Modular CSS features discrete, isolated components. When you change one, you don’t accidentally break an unrelated part of the app. If you’re unfamiliar with the concept of Modular CSS, [this presentation](https://youtu.be/Ty5jtMZXbmk) is a good starting point.

2. Documentation is critical, and comes in the form of comments. Provide context and explain *why* you’re doing something, not *what* you’re doing. The code you write today may live for a very long time, so be considerate of the person who has to revisit it a year from now — it might be you!

3. We favor clarity over cleverness. If there are two approaches to solving a problem, prefer the one that more people will understand. Ideally, any developer at the company should be able to work in our files. If your code is difficult to parse, you’ll find people working around it instead of using it.

Before you write CSS for any Tempest product, please take the time to read this document carefully. If you haven’t already, you should also check out Jonathan Snook’s [SMACSS](https://smacss.com/) book. It’s a quick read, and the bulk of our philosophy is based on his approach. If you have any questions, please ask in the “UI Dev” or “Tempest” rooms in Hipchat — We’re happy to help!

**Living Standard:** This document is not finalized, nor will it ever be. CSS is an ever-changing standard, and we will update our best practices as it evolves. If you find something you believe should be changed, make a pull request and explain your reasoning. The rest of the team will discuss it and a decision will be made. Please don’t schedule a meeting, as those tend to be contentious and dominated by whoever talks the loudest. Carefully considered discussion here can help to avoid snap judgements and entrenched opinions.

**Code Reviews:** If you were sent here because some CSS you wrote violates these standards, please read carefully and update your code to match. If you don’t agree with these guidelines, feel free to make a pull request as described above. However, in the meantime please change your code. “Waiting for the code standards to change” is not a good reason to delay a project.

## Class Naming Conventions

We follow a strict naming conventions for our modular CSS classes. If you’ve never used a BEM or SMACSS-like naming convention before, it can seem like overkill. The primary advantage, however, is that your CSS becomes [self-documenting](http://cssguidelin.es/#naming-conventions). Done properly, and we can clearly see which classes are and are not related to each other, and how; we know what classes we can’t use outside of the scope of a module; and we know which classes we may be free to reuse elsewhere.

* **Module** — `.m-module-name` — Modules are the core elements that make up the content and functionality of the page. For example, `.m-story` is the class name for a story (we use this as a generic term for post or article). These generally represent the core content of each element on the page.

* **Module Component** — `.m-module-name--component` — Components are the child elements inside a self contained module. For example, `.m-story--copy` is the class name for the body text that is one part of the story module.

* **Module Modifier** — We have two types of module modifiers: global and namespaced.

	* **Global Modifiers** — `.mm-modifier-name` — This is used for small chainable changes to a module. It’s possible to combine multiple modifiers with a module. In general, these rules are pretty universal so they can be reused across different types of modules. An example is `.mm-has-svg`, a generic modifier we give to elements that contain svg.

	 _Note: In the CMS, we call these **Helper** classes and use the prefix `.h-` to distinguish from scoped module modifiers. For example, `.h-uppercase` would change an element to all-caps._

	* **Namespaced Modifiers** — `.mm-module-name--modifier-name` — Contain rules that create a style variation of a module. Since they wouldn’t be useful elsewhere, we name them based on the module they’re intended for. An example is `.mm-brand-engagement--inverted`, which affects the colors of the brand engagement module.

* **Layout** — `.l-layout-name` — Layout classes are exclusively used to define the positioning an element in a predefined way, such as full-bleed or breakout. They can even handle absolutely positioned elements like a modal or toaster. Used to define things like margins, position, or floats, but probably not padding and definitely not “visual” styles like color, background, etc. A good example of this is the wrapper class `.l-full-width` which is used as a parent class on any page of any content type that is meant to expand to full width.

* **State** — `.is-state-name` — State classes are used to represent “temporary” states of a module, not changes to the module. Examples would be `.is-disabled`, `.is-read`, `.is-selected`. These are classes that generally are added and removed with JavaScript.

## Order of Properties

We use a standard order for properties in a ruleset. By following this order, our code become more standardized and easier to understand. It also avoids possible cascade problems (e.g., `@include` statements can be overridden by regular styles which can be overridden by nested pseudo-classes and media queries).

This can get a bit confusing sometimes. When it doubt, remember that the goal is to first define any included styles, followed by regular styles, followed by any modifier styles, followed by child selectors (whose code would follow this same order).

1. #### Included Code

	1. Local `$variables`
		* Local variables should really only be declared for mixins or functions. Most variables should be kept in the main variables partial, since variables in Sass are global.
		* However, if you have a set of variables that will only be used in a certain partial, declare them at the top of that file and prefix their names to avoid confusion in the global namespace. e.g., `$m_module_name_height`.

	1. `@extend` statements
		* Avoid extends in favor of mixins. Extends offer no benefits over mixins, and can cause [specificity problems](http://csswizardry.com/2016/02/mixins-better-for-performance/).

	1. `@include` statements
		* Knowing right off the bat that this class inherits another whole set of rules from elsewhere is good. Another benefit is that overriding styles for that inherited set of rules becomes much easier.

2. #### Alphabetized Regular Styles
	* Adding regular styles after the `@extend` and `@include` statements allows us to override those properties, if needed.
	* Alphabetizing is helpful for a large team because it standardizes location of properties. (You can quickly alphabetize declarations in Sublime Text by selecting several lines of code and pressing F5.)

3. #### Modifiers
	1. Pseudo-classes (`:hover`), attribute selectors (`&[type="input"]`), and chained classes (`&.modifier`)
		* These directly modify the parent selector so we declare them before other nested selectors.

	1. Nested media queries
		* These come after regular styles, pseudo-classes, etc., so they can override them.

4. #### Child Selectors

	1. Pseudo-elements (`::before`)
		* Pseudo-elements are actually generated children, and so should be treated like any other nested selector.

	2. Nested selectors
		* As a rule, **if a selector will work without it being nested then do not nest it.**
		* There shouldn’t be many nested selectors. Our naming convention means that we don’t need to nest selectors for namespacing. Before you nest a selector, consider if it would be better as a module component class.
		* Any nested selectors could contain their own nested pseudo-classes, pseudo-elements and media queries, following the rules above.

## Formatting Rules

These formatting rules should be reflected in a `.stylelintrc` file in the root of your project. (Our standard `.stylelintrc` file can be found in [stylelint-config-saymedia](https://github.com/saymedia/stylelint-config-saymedia).) This file can be read by your text editor (for example, see the SublimeLinter project), or run as part of the existing project lint tests to enforce our house style.

* Place each selector and declaration on their own line.

* Include a single space before the opening brace (`{`) of a ruleset.

* Include a single space after the colon (`:`) of a declaration.

* Include a semicolon (`;`) at the end of every declaration.

* Place the closing brace (`}`) of a ruleset on a new line.

* Separate each ruleset with a single blank line.

* Separate variables in a ruleset from declarations with a single blank line.

* Indent declarations and nested rulesets by four (`4`) spaces.

* Use lowercase and longhand hex values, e.g., `#aaaaaa`.

* Quote attribute values in selectors, e.g., `input[type="checkbox"]`.

* Avoid specifying units for zero-values, e.g., `margin: 0`.

* Always prefix values with a leading zero for clarity, e.g., `0.5em`, not `.5em`.

* Don’t add trailing zeros, e.g., `2em` not `2.0em`.

* Include a space after each comma in comma-separated property or function values, e.g., `rgba(255, 0, 0, .5)`.

* Class names should be [lowercase and use hyphens](http://csswizardry.com/2010/12/css-camel-case-seriously-sucks/). No underscores or camelcase.

* Functions, variables and mixin names should be lowercase and use underscores, to make them distinct from class names. No hyphens or camelcase.

* Avoid shorthand syntax unless you’re explicitly setting all the available values, otherwise you may [inadvertently override a value](http://csswizardry.com/2016/12/css-shorthand-syntax-considered-an-anti-pattern/) you didn’t intend to.

### Example

```scss
//
// Bad Example
//
// 1. Selectors should be on their own line
// 2. Includes should come before standard rules
// 3. Use min-width media queries for mobile-first designs
// 4. pseudo & chained classes should come after standard rules
// 5. Nested selectors should come after nested media queries
// 6. This rule should be at the top with the other rules
// 7. Too many !importants, time to refactor
// 8. This should be in a nested media query for .m-feed--loader
// 9. Collapse blank lines between closing braces
//
.foo, .bar { // 1
    width: 110%;

    @include hyphenate(); // 2

    @media only screen and (max-width: $screen_small) { // 3
        width: 100%;
    }

    &:hover { // 4
        color: red;
    }

    &.mm-has-svg { // 4
        @include svg_sizer();
    }

    .m-feed--loader { // 5
        margin: 0 !important;
    }

    margin-top: -20px !important; // 6

    @media only screen and (min-width: $screen_large) {
        margin-top: -30px !important; // 7

        .m-feed--loader { // 8
            display: none;
        }

    } // 9

}

//
// Good Example
//
// 1. Selectors on their own lines
// 2a. First come mixins
// 2b. Then alphabetical rules
// 2c. Then nested pseudo-classes, chained selectors
// 2d. Then nested media queries
// 2e. Last come nested selectors (& their own nested selectors)
// 3. Comment when code is only there to override inherited styles
// 4. Use lots of variables for a single source of truth
//
.foo,
.foo-bar,
.baz { // 1
    @include font_ui(2.0); // 2a
    line-height: 1;        // override font_ui line-height // 3
    margin-top: $length;   // 4
    width: 100%;           // 2b

    &:hover { // 2c
        color: red;
    }

    &.mm-has-svg { // 2c
        @include svg_sizer();
    }

    @media only screen and (min-width: $screen_medium) { // 2d
        width: 100%;
    }

    @media only screen and (min-width: $screen_large) { // 2d
        margin-top: $length * 2; // 4
    }

    .m-feed--loader { // 2e
        margin: 0;

        @media only screen and (min-width: $screen_large) {
            display: none;
        }
    }
}
```

## Best Practices

* Use `$variables` whenever possible, especially for repeated colors and numbers. This creates a single source of truth for faster updates.

* Don’t declare z-index values as raw numbers. Instead, use a Sass list of z-indexes and a function to retrieve named z-index values. This makes it easy to adjust the stacking order or add new values. ([learn more](https://www.smashingmagazine.com/2014/06/sassy-z-index-management-for-complex-layouts/))

* Don’t override styles for one module inside another. Modules, by definition, should be self-contained. Use a module modifier instead. This avoids any cross-contamination and specificity problems, and the new modifier can be reused elsewhere. ([learn more](http://simurai.com/blog/2015/05/11/nesting-components))

* Don’t directly add vendor prefixes. They make our code more brittle and difficult to maintain. Instead, write current CSS and then use a tool to add the vendor prefixes where needed. We use [Bourbon](http://bourbon.io/docs/) mixins in Phoenix and [Autoprefixer](https://github.com/postcss/autoprefixer) in the CMS.

* Don’t use Sass nesting to construct class names ([like this](https://jonsuh.com/blog/sass-bem-selector-and-trailing-ampersand/)). It makes it more difficult to search for classes. ([learn more](http://markdotto.com/2015/07/20/css-nesting/#bem-nesting-with-))

* Avoid nesting. It increases specificity and makes styles harder to troubleshoot. Our module naming convention should mean you rarely need to do this. If you must, make sure to clearly comment why nesting was necessary. ([learn more](http://cssguidelin.es/#nesting))

* Don’t use ID or element selectors. Using class selectors keeps our specificity tree low, which avoids problems when you need to override something. ([learn more](http://cssguidelin.es/#ids-in-css))

* Don’t use `!important` to solve specificity problems. It should only be used for styles that are meant to be immutable, such as helper classes. ([learn more](http://csswizardry.com/2016/05/the-importance-of-important/))

### Commenting

* Code comments are easier to create, find, and update than external documentation.

* Avoid writing `TODO` and `FIXME` comments. We have a robust ticketing system, which is a better place to keep track of those items.

* Clearly document code that exists for the sole purpose of overriding inherited styles.

* Always use Sass-style comments (`//`). CSS comments (`/* */`) are stripped from our compiled files, so they serve no purpose.

* Inline comments should be kept short. If writing more than 2-3 words, use the [reverse footnote style](http://cssguidelin.es/#low-level) instead. Add an inline comment with a number (`// 1`) referencing a footnote in a larger comment block at the top of the ruleset.

### Third-Party Add-Ons

* **Vendor prefixing**: Phoenix uses [Bourbon](http://bourbon.io/) for vendor prefix mixins, but we’d like to switch to [Autoprefixer](https://github.com/postcss/autoprefixer), which the CMS is already using.

* **Framework:** Phoenix doesn’t use a framework. CMS uses [Bootstrap](https://getbootstrap.com/).

* **Normalize.css:** Both Phoenix and CMS use [Normalize.css](https://necolas.github.io/normalize.css/).

* **Icons:** Phoenix uses a custom icon font called Say Glyphs. CMS uses [FontAwesome](http://fontawesome.io/), but is looking to drop it in favor of SVG icons.

## References

This document owes a great deal to Harry Robert’s [CSS Guidelines](http://cssguidelin.es/), and Jonathan Snook’s [SMACSS](https://smacss.com/) book.

In addition, here is a small list of sites, documents, and books that informed the creation of our code standards. Be careful, while some of the advice is still applicable, many of these date back to 2012, and best practices have evolved since then.

* [Idiomatic CSS](https://github.com/necolas/idiomatic-css) by Nicolas Gallagher

* [Code Guide](http://codeguide.co/) by Mark “@mdo” Otto

* [SASS Guidelines](https://sass-guidelin.es/) by Hugo Giraudel

* [BEM](https://en.bem.info/methodology/) by Yandex

* [MindBEMding – getting your head ’round BEM syntax](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) by Harry Roberts

* [Battling BEM: Common Problems And How To Avoid Them](https://www.smashingmagazine.com/2016/06/battling-bem-extended-edition-common-problems-and-how-to-avoid-them/) by David Berner

* [SMURF](https://github.com/railslove/smurfville/wiki) by Jakob Hilden ([learn more](http://smurf-presentation.herokuapp.com/))

* [The Road to Reusable HTML Components](https://www.smashingmagazine.com/2012/10/road-reusable-html-components/) by Niels Matthijs

* [Taking Sass to the Next Level with SMURF and @extend](https://gist.github.com/paulkogel/079adfd9ef604ee509fc) by Jakob Hilden

* [OOCSS + Sass = The Best Way to Write CSS](https://ianstormtaylor.com/oocss-plus-sass-is-the-best-way-to-css) by Ian Storm Taylor

### Historic Document Archive

Don’t read these. These links will only work for Say employees, and are previous attempts at documenting our code standards. They're archived here for historical purposes, but should be considered deprecated and out-of-date compared to this document.

* [Phoenix CSS Framework Docs](https://docs.google.com/spreadsheets/d/1QWh_bOsuL9LKI687rz10mgGa0vMtCJnln7iPjXLTuCA/edit#gid=0) — Spreadsheet explaining the core concepts of the Phoenix CSS framework, and lists of class names, variables, etc.

* [CSS at Say Media](http://www.saydaily.com/2014/09/css-at-say-media) — Blog post summarizing how we handle CSS in Phoenix.

* [Harry Roberts Phoenix Code Review](https://docs.google.com/document/d/1IerQ1BvoZ5JtmOJG37YubN2IpVlLuNrwn__SId3T5AQ/edit) — In October 2014, we hired Harry Roberts to do a code review on the Phoenix CSS. The results are informative, but should be taken with a grain of salt given how much the code base has changed since he reviewed it.

* [Phoenix CSS Framework Wiki](https://saywiki.atlassian.net/wiki/display/devel/Phoenix+CSS+Framework) — Mostly a collection of links to other documents circa 2014. These should be regarded as out-of-date:

  * [Default Font Fallbacks](https://saywiki.atlassian.net/wiki/display/devel/Default+Font+Fallbacks)

  * [Embedded Objects & Videos](https://saywiki.atlassian.net/wiki/display/devel/Embedded+Objects+and+Videos)

* [CSS Styleguide Wiki](https://saywiki.atlassian.net/wiki/display/devel/CSS+Styleguide) — Created by Scott after he started refactoring the CMS, and never really updated. Should be considered completely irrelevant.
