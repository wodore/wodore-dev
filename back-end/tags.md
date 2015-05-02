Tags
======

Tags are used globally to define categories for points, routes, and route groupes.

Tags are saved globally and can my managed by tag manager.
Everybody can create new tags and assign a color and a icon to it. If later someone else uses the same tag it also uses the same properties (icon, color).
The problem with this approach is if someone does not like the settings from someone else.
For this reason it is possible to change the tag setting on a group level.
This means each tag is saved globally and per group with special settings.

The tags also save related tags for better tag suggestions.

Here is how a tag looks like:

```javascript
{ "tag": {
    "id" : "TagName",
    "parent" : "TagName",
    "options" : {
      "color" : "hex color code",
      "icon" : "icon name",
      "icon-type" : "font/svg/png"
    },
    "relatedTo" : [{"id":"TagName", "cnt":#relations},...],
    "replaceBy" : "TagName",
    "approved" : false,
    "approveRequest" : false,
  }
}
```


