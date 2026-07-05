---
title: CSS Impurtant Concepts
tags:
  - css
  - styling
  - cssom
  - paint
draft: false
---

# Module 1 — CSS Custom Properties (Real Variables)

Most beginners think CSS variables are just variables.

They aren't.

They participate in the cascade.

```
:root{    --primary: royalblue;}button{    background: var(--primary);}
```

Unlike Sass variables:

```
$primary: blue;
```

Sass variables disappear after compilation.

CSS variables exist **at runtime**.

That means JavaScript can change them instantly.

```
document.documentElement.style.setProperty(    "--primary",    "red");
```

The browser repaints without rewriting stylesheets.

---

## Variables inherit

```
body{    --color: blue;}.card{    color: var(--color);}
```

Every descendant inherits `--color`.

---

Override locally

```
.card{    --color:red;}.title{    color:var(--color);}
```

Now only that subtree changes.

This is extremely useful for theming.

---

## Variables are lazy

This surprises many developers.

```
:root{    --size:20px;}.box{    width:var(--size);}
```

The browser doesn't replace `var(--size)` during parsing.

It resolves it later when computing styles.

This allows:

```
--size:calc(100vw - 20px);
```

Variables can depend on other variables.

---

# Sharing data between HTML and CSS

This is one of the most powerful areas of modern CSS.

---

## Method 1 — CSS Variables

```
<div class="card"></div>
```

```
.card{    --rotation:20deg;    transform:rotate(var(--rotation));}
```

---

JavaScript

```
card.style.setProperty(    "--rotation",    "70deg");
```

No classes.

No inline transform.

Just changing data.

---

## Method 2 — Data Attributes

HTML

```
<div data-theme="dark">
```

CSS

```
[data-theme="dark"]{    background:black;}
```

Now HTML communicates state to CSS.

Very common in component libraries.

---

## Method 3 — Attribute Values (`attr()`)

Modern CSS allows reading attributes.

```
<div data-size="40">
```

```
.box{    width:attr(data-size px);}
```

Support for typed `attr()` outside generated content is still evolving across browsers, so check compatibility before relying on it in production.

---

## Method 4 — Environment Variables (`env()`)

Browser provides data.

```
padding-top:env(safe-area-inset-top);
```

Used for iPhones.

---

## Method 5 — Container Queries

Instead of viewport size...

Ask the parent.

```
@container(width>600px){.card{display:flex;}}
```

Huge improvement over media queries.

---

# CSS Functions

CSS has far more functions than most people realize.

---

## calc()

```
width:calc(100% - 80px);
```

---

## min()

```
width:min(800px,90%);
```

Never exceed 800px.

---

## max()

```
font-size:max(16px,2vw);
```

---

## clamp()

One of the best CSS functions.

```
font-size:clamp(16px,2vw,32px);
```

Meaning

Never smaller than 16.

Never bigger than 32.

Scale smoothly in between.

---

## color-mix()

```
background:color-mix(in srgb,red 40%,blue);
```

Mix colors without preprocessors.

---

## light-dark()

```
color:light-dark(black,white);
```

Responds to the active color scheme.

---

## CSS Trigonometry

Yes.

CSS has math.

```
rotate:sin(30deg);
```

Also

```
sin()cos()tan()asin()acos()atan()pow()sqrt()hypot()
```

Useful for procedural layouts and animations.

---

# Advanced Selectors

---

## :is()

Instead of

```
button,a,input{...}
```

Use

```
:is(button,a,input){...}
```

It takes on the specificity of its most specific argument.

---

## :where()

Looks similar.

```
:where(button,a,input)
```

Difference:

Specificity = **zero**.

Very useful for reusable component libraries.

---

## :has()

The famous parent selector.

```
.card:has(img){
```

Now style parent based on child.

Examples

```
form:has(:invalid)nav:has(.active)article:has(video)
```

This changed CSS forever.

---

## nth-child formulas

Not just

```
:nth-child(2)
```

But

```
:nth-child(3n+1)
```

or

```
:nth-child(-n+5)
```

or

```
:nth-child(4n-2)
```

Powerful pattern matching.

---

## Attribute Selectors

```
[href^="https"][href$=".pdf"][href*="google"][lang|="en"][data-state="open"]
```

Extremely useful.

---

# Cascade Layers

One of the best modern CSS features.

```
@layer reset;@layer components;@layer utilities;
```

Now precedence becomes

```
Reset↓Components↓Utilities
```

Instead of fighting specificity.

---

# CSS Nesting

Finally native.

```
.card{background:white;& .title{font-size:20px;}&:hover{background:black;}}
```

No preprocessor required.

---

# @scope

Limit styles.

```
@scope (.card){button{color:red;}}
```

Styles stay inside that scope.

Great for reusable components.

---

# Container Queries

Instead of

```
@media
```

Ask the component.

```
@container
```

Responsive components become independent.

This is a paradigm shift.

---

# Logical Properties

Instead of

```
margin-left
```

Use

```
margin-inline-start
```

Automatically adapts to left-to-right and right-to-left writing modes.

Also

```
padding-inlinepadding-blockborder-inlineinset-inlinemargin-block
```

---

# CSS `contain`

Tell browser

"This component is isolated."

```
contain:layout;
```

Or

```
contain:layoutpaintstylesize
```

Improves rendering performance.

---

# content-visibility

Massive performance improvement.

```
content-visibility:auto;
```

Browser skips rendering off-screen content until needed.

Ideal for long pages.

---

# will-change

Tell browser

"I'm about to animate."

```
will-change:transform;
```

Use sparingly—overusing it can increase memory usage because it may promote elements to separate compositing layers.

---

# Stacking Context

One of CSS's hardest concepts.

Many developers think

```
z-index:999999;
```

always wins.

It doesn't.

A new stacking context isolates its descendants, so an element with a huge `z-index` can still appear behind an element in a different stacking context.

You should understand:

- What creates a stacking context (`position` with `z-index`, `opacity < 1`, `transform`, `filter`, `isolation`, etc.)
- Paint order within a stacking context
- How nested stacking contexts interact

This explains most "why isn't my z-index working?" bugs.

---

# Formatting Contexts

CSS has multiple layout models.

```
Block Formatting Context (BFC)Inline Formatting Context (IFC)Flex Formatting ContextGrid Formatting Context
```

A Block Formatting Context isolates layout behavior. For example, creating one with `display: flow-root` prevents floats from escaping and stops vertical margin collapsing with surrounding elements.

---

# CSS Houdini

One of the least-known parts of CSS.

You can register your own CSS properties.

```
@property --rotation{    syntax:"<angle>";    inherits:false;    initial-value:0deg;}
```

Now

```
transition:--rotation
```

works properly because the browser knows it's an angle, not just a string.

Houdini also exposes APIs for custom painting, layout, and typed values, enabling capabilities that previously required JavaScript or weren't possible at all.

---

# Advanced topics most developers never study

|Topic|Why it matters|
|---|---|
|CSSOM construction|How browsers parse CSS into objects before style calculation|
|Cascade algorithm|The full decision process: origin → importance → layer → specificity → order|
|Computed vs Used vs Actual values|Explains why `getComputedStyle()` and final layout values can differ|
|Selector matching|Browsers typically match selectors from right to left for efficiency|
|Style recalculation|Understanding what triggers expensive recalculations|
|Grid track sizing algorithm|The algorithm behind `fr`, `minmax()`, and auto tracks|
|Flexbox sizing algorithm|How `flex-grow`, `flex-shrink`, and `flex-basis` are resolved|
|Paint order|Determines which element is painted on top before compositing|
|Compositing & GPU layers|Why some animations are smoother than others|
|CSS Typed OM|A structured JavaScript interface to CSS values instead of strings|