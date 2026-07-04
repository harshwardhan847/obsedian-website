---
title: Rendering HTML in Browser
sub-title: sub title
tags:
  - html
  - browser
  - rendering
draft: false
---
## Browser Structure
![[images/browser_structure.png]]


## HTML

if we do getElementByTagName then we will get a NodeList not Array or javascript native data type.

Now, on each element we have 2 major operations:-
1. Display - displaying elements on UI
2. Interact - handle click events

## HTML Parsing Process

- Load File (HTML).     - **DOCUMENT** 
- Raw Bites (conversion of HTML to 0,1)
- Character encoding ( UTF-8 ) -> Tokenisation -> h1, p, html, body
- Converts to an Object - **OBJECT**
```
	{
		tag:h1
		title:Some
		Data/value:youtube
	}
```
- Relation
- Nodes :- Objects converts to a model in tree data structure - **MODEL**
- Here comes the word DOM(DOCUMENT OBJECT MODEL)

## CSS Parsing

Raw -> Character -> Token -> Object -> Relation -> Model (CSSOM)


## Render Tree

We have :- **DOM**, **CSSOM** => Browser engine -> 
mathematical calculations for UI pixel perfect rendering =>

Painting =>

<script/> (whenever browser sees this script tag browser stops rendering, DOM)

**Note** :- But JS Execution will be **halted** if CSSOM is not ready

## Loading JS 

```
<script src="script.js" defer></script>
```

| Loading Type            | HTML Parsing Status During Download | Execution Timing                                      | Main Use Case                                          |
| ----------------------- | ----------------------------------- | ----------------------------------------------------- | ------------------------------------------------------ |
| **Normal** (`<script>`) | Paused (Blocked)                    | Immediately after download finishes                   | Critical scripts that must run before anything renders |
| **Defer** (`defer`)     | Continues (Parallel)                | **After** HTML parsing finishes, in order             | Scripts that depend on the DOM or other scripts        |
| **Async** (`async`)     | Continues (Parallel)                | **Immediately** after download finishes (Pauses HTML) | Independent scripts like analytics, ads, or trackers   |


## Reference Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/5rLFYtXHo9s?si=CJiE4IY83MF6DLWQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



