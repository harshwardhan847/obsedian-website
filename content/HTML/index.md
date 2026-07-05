---
title: HTML
tags:
  - html
  - browser
  - rendering
  - tokenisation
  - tokenization
draft: false
---
# How does the browser actually understand HTML?

That's where browser internals, parsing algorithms, rendering, accessibility, performance, and the HTML specification come in.


# Here are the advanced HTML concepts most developers never study
---

# 1. HTML Tokenisation ⭐⭐⭐⭐⭐

The browser never reads HTML tag-by-tag.

Instead it reads **one character at a time**.

```
<div>Hello
```

Every character goes through a huge **state machine**.

Example

```
<
```

↓

```
Tag Open State
```

↓

```
div
```

↓

```
Start Tag Token
```

↓

```
Emit Token
```

This process is called

> **Tokenization**

The tokenizer alone contains **60+ states** in the HTML specification.

Examples

```
Data StateTag Open StateAttribute Name StateAttribute Value StateDOCTYPE StateComment StateScript Data StateCDATA State
```

---

Why is it important?

Because browsers can recover from invalid HTML.

Example

```
<divclass="box"id="hello"
```

Notice the missing `>`.

The tokenizer doesn't immediately fail. It keeps reading until it can recover.

This is why browsers almost never crash on malformed HTML.

---

# 2. Tree Construction ⭐⭐⭐⭐⭐

Tokens are not the DOM.

Example

Tokenizer outputs

```
StartTag(html)StartTag(body)StartTag(div)TextEndTag(div)EndTag(body)
```

Another algorithm converts tokens into DOM nodes.

```
Tokens↓Tree Builder↓DOM Tree
```

The tree builder has special rules.

Example

```
<table><div><tr>
```

A browser silently changes it into

```
table└── tbody     └── trdiv moved outside
```

Even if you never wrote `<tbody>`.

The browser inserts it automatically.

---

# 3. HTML Parser is Fault Tolerant ⭐⭐⭐⭐⭐

HTML isn't XML.

Browsers repair invalid markup.

Example

```
<p><div>Hello
```

The browser changes it into

```
<p></p><div>Hello</div>
```

Another example

```
<li><li><li>
```

Browser automatically inserts

```
<ul><li><li><li></ul>
```

This automatic fixing is called

> **Error Recovery**

---

# 4. Insertion Modes ⭐⭐⭐⭐⭐

The parser doesn't always parse HTML the same way.

It changes behavior depending on where it is.

Example modes

```
Before HTMLBefore HeadIn HeadAfter HeadIn BodyIn TableIn RowIn CellAfter Body
```

Each mode has different parsing rules.

This is why tables behave differently than normal HTML.

---

# 5. Foster Parenting Algorithm ⭐⭐⭐⭐⭐

One of HTML's weirdest algorithms.

Example

```
<table><div>Hello</div></table>
```

The browser cannot place a `<div>` directly inside a `<table>`.

Instead

It moves it outside.

```
divtable
```

This movement is called

> Foster Parenting

Very few frontend developers know this exists.

---

# 6. DOM Nodes are Objects ⭐⭐⭐⭐⭐

HTML

```
<div>Hello</div>
```

Doesn't become

```
text
```

Instead

```
HTMLDivElement↓HTMLElement↓Element↓Node↓EventTarget↓Object
```

Inheritance chain

```
Object└── EventTarget      └── Node            └── Element                  └── HTMLElement                        └── HTMLDivElement
```

That's why

```
querySelector()appendChild()addEventListener()
```

come from different parent classes.

---

# 7. NamedNodeMap

People know

```
element.attributes
```

Few know its type.

```
NamedNodeMap
```

Not

```
Array
```

Not

```
Object
```

Not

```
Map
```

It is an old browser collection.

---

# 8. Live Collections vs Static Collections ⭐⭐⭐⭐⭐

```
getElementsByTagName()
```

returns

```
HTMLCollection
```

Live.

```
querySelectorAll()
```

returns

```
NodeList
```

Static.

Example

```
const items =document.getElementsByTagName("div");
```

Add another div.

The collection updates automatically.

```
Length before5Length after6
```

Without querying again.

---

# 9. DocumentFragment ⭐⭐⭐⭐⭐

One of the biggest performance optimizations.

Instead of

```
appendappendappendappend
```

Build everything off-screen.

```
Fragment↓append children↓append once
```

One DOM insertion instead of hundreds.

---

# 10. HTML Parsing Stops for JavaScript ⭐⭐⭐⭐⭐

Example

```
<script>document.write(...)</script>
```

The parser pauses.

```
HTML Parser↓Script↓Stop↓Execute JS↓Continue
```

Because JavaScript may modify the HTML.

---

# 11. Speculative Parser

Modern browsers don't fully stop.

While the main parser waits

A second parser starts discovering

```
CSSImagesFontsScripts
```

It requests them early.

Huge performance improvement.

---

# 12. DOM Isn't the Render Tree

People think

```
DOM=What is rendered
```

Wrong.

Example

```
<div hidden></div>
```

Exists in DOM.

Not in Render Tree.

Example

```
display:none
```

DOM

Yes.

Render Tree

No.

---

# 13. Accessibility Tree

There isn't only one tree.

There are three major trees.

```
DOM Tree↓Render Tree↓Accessibility Tree
```

Screen readers don't read the DOM.

They read

```
Accessibility Tree
```

---

# 14. HTML Loading Priority

Resources don't have equal priority.

Browser roughly schedules:

```
HTML↓CSS↓Blocking JS↓Fonts↓Images↓Lazy Images
```

Modern browsers dynamically adjust priorities based on layout, viewport, and heuristics.

---

# 15. Preload Scanner

While parsing

Browser scans ahead.

Example

```
<img><link><script><video>
```

Downloads begin before parsing finishes.

This saves hundreds of milliseconds.

---

# 16. Custom Elements Upgrade Process

When browser encounters

```
<my-button>
```

Initially

```
Unknown Element
```

Later

```
customElements.define()
```

Browser upgrades it.

```
HTMLElement↓MyButton
```

Without recreating the DOM.

---

# 17. Shadow DOM

Actually creates another DOM tree.

```
Document└── App     └── Video Player           Shadow Root               button               progress               controls
```

CSS outside usually can't affect nodes inside the shadow root, and the shadow tree isn't exposed through normal DOM traversal from outside. This is how many built-in elements and web components encapsulate implementation details.

---

# 18. HTML Parsing is Streaming

Browser doesn't wait.

```
Download↓Parse↓Render↓Download More↓Continue
```

Everything happens simultaneously.

---

# 19. DOM Mutation Algorithms

```
appendChild()remove()replaceChild()
```

Don't simply manipulate arrays.

They update

- parent pointers
- sibling pointers
- live collections
- mutation observers
- custom element callbacks
- layout invalidation
- accessibility information
- style recalculation state

One DOM operation can trigger a cascade of internal updates.

---

# 20. DOM Memory Model

Each node stores far more than:

```
TagText
```

Internally, browsers maintain structures that include information such as:

- Parent reference
- Child references
- Sibling references
- Attributes
- Namespace
- Event listener registrations
- Style data (or links to computed style)
- Layout object references
- Accessibility metadata
- Flags used by the parser and renderer

A single element can therefore consume much more memory than its HTML source suggests, which is why very large DOM trees can hurt both memory usage and performance.