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

## HTML parsing process

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
- 

