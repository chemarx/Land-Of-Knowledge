up:: [[Home]]
tags:: #map/view

# The Inbox
Nơi lưu trữ những bài viết mới nhất và chưa biết nên xếp vào đâu.

``` dataview
TABLE WITHOUT ID
 file.link as "Encounters and new notes",
 (date(today) - file.cday).day as "Days alive"

FROM "+ Encounters" and -#on/readme 

SORT file.cday asc

LIMIT 20
```
