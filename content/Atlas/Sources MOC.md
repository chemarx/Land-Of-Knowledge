up:: [[Home]]
tags:: #map 

# Sources MOC

## A data view from the Sources folder
This is a simple data view pulling anything from the **Sources** folder.
```dataview
table tags
from "Sources" and -#on/readme 
sort file.name asc
```