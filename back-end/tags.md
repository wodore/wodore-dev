Tags
======

Tags are used globally to define categories for points, routes, and route groupes.

Tags are saved globally and can be managed by tag manager.
Everybody can create new tags and assign a color and a icon to it. If later someone else uses the same tag it also uses the same properties (icon, color).
The problem with this approach is if someone does not like the settings from someone else.
For this reason it is possible to change the tag setting for a user group.
This means each tag is saved globally and per group with special settings.

The tags are accessed through the model class `Tags` and `Tag`.
`Tags` manages all tags whereas `Tag` is the class for one tag.
This are the public methods:

`Tag` Methods
-------------------
- `constructor(self, name, style={}, group=[], status=LOCAL, counter=1)`
- `get_name()`
- `set_name(name)`
- `set_style(style,group)`
- `set_related_to(tag_names)`
- `remove_related_to(tag_names)`
- `get_status(status)`
- `set_status(status)`
- `get_counter()`
- `_incr_counter(incr=1)`
- `_decr_counter(decr=1)`

A group is an array of groups, if the array is empty the global tag is used. 
The simplest case is to have only one group: `[group1]` but more levels are supported: `[group1,group1a,group1aE]`.
The `Tag` class makes sure that all tags are updated correctly (for every grup level). For example if the tag counter of `group1aE` is increased, all the other counter needs to be increased as well.

`Tags` Methods
---------------
- `get_suggestions(tags,limit,scope, json=false)` scope is either global, group or both
- `get_tags(group, min_counter, max_counter, order, json=false)`
- `add_tags(tags, auto_add_related_to=true, auto_counter = true, json=false)` 
- `remove_tags(tags, auto_add_related_to=true, auto_counter = true, json=false)` 
- `change_tags(add_tags , remove_tags, auto_add_related_to=true, auto_counter = true, json=false)` is used for updates as well

`tags` is either a string array with tag names, an array with `Tag` objects, or if `json=true` a array with json tag objects.


Tag Databases
--------------
Two databases are used to store tags, one for all tags and one for the tag relationships (used for suggestions).
The model for the two DBs are `tag_db` and `tag_relation_db`.

### `tag_db` Entry
```javascript
id        : "TagName",
parent    : "UserGroupKey", // if it is a usergroup tag it needs a parent
color     : "hex color code",
icon      : "icon name",
icon_type : "font/svg/png"
counter   : 0, // Tag counter (how oftern is it used)
approved  : false,        // The tag is approbed as public tags
```
### `tag_relation_db` Entry
```javascript
tag_key    : "Tag key", // consists of parent and id
related_to : "Tag key", // consists of parent and id
counter   : 0, // Tag counter (how oftern is it used)
```
In future two more databases are used. One called `tag_approve_request_db` which saves all approve requests for a tags.
And another called `tag_replace_by_db` where tag replacements can be saved, for example for different languages.

Example
----------
The working user group is called *EasterTour* which has the key `xyz`.
The following three points are added:
- *Bus station*, tags: `bus, public transport, over 2000, break`
- *Peak 4*,tags: `peak, over 2000`
- *Sleep Good*, tags: `accommodation, tent, over 2000, break`

We assume the tag `peak` was already used 8 times for another tour (key: `an_key`).
Each tag which has not already a global entry is added as a global tag and for the user group.
This results in the following table (`tag_db`)

| id               |  parent  | color   | icon      | icon_type | counter | approved  
| ---------------- | -------- | ------- | --------- | --------- | ------- | -------
| peak             |          | #345DD2 | mountain  | font      | 10      | true
| peak             | an_key   |         |           |           | 8       | 
| bus              |          | #345E32 | bus       | svg       | 1       | false
| bus              | xyz      |         |           |           | 1       |  
| public transport |          | #345E32 | train     | svg       | 1       | false
| public transport | xyz      |         |           |           | 1       |  
| break            |          | #333E32 | fire      | font      | 2       | false
| break            | xyz      |         |           |           | 2       |  
| peak             | xyz      |         | peaks     | svg       | 1       |  
| over 2000        |          | #345632 | peak      | svg       | 3       | false
| over 2000        | xyz      |         |           |           | 3       |  
| accommodation    |          | #345D32 | hut       | svg       | 1       | false
| accommodation    | xyz      |         |           |           | 1       |  
| tent             |          | #34FFD2 | camping   | svg       | 1       | false
| tent             | xyz      |         |           |           | 1       |  

The  `tag_relation_db` looks like:

| tag\_key         |  related\_to     | counter   
| ---------------- | ---------------- | ------- 
| peak             | over 2000        | 1
| xyz/peak         | over 2000        | 1
| over 2000        | peak             | 1
| xyz/over 2000    | peak             | 1
| over 2000        | break            | 2
| xyz/over 2000    | break            | 2
| over 2000        | bus              | 1
| xyz/over 2000    | bus              | 1
| ...              | ...              | ...
| break            | public transport | 1
| xyz/break        | public transport | 1
| break            | over 2000        | 1
| xyz/break        | over 2000        | 1
| ...              | ...              | ...

Not every relationship is shown in the table.


Concerns
---------
- Different groups with different icons (for the same tag). If this happens the icon of the current *active* group will be used.
- Different languages and tags in general which mean the same. For example `hut` and `huette` or even `hütte`. If the tag is only used for one user group it is no problem, but for future extension it would be nice to have unique public tags. For this is the field `replaceBy`. If the tag is not  `approved` for the public it needs to be replaced by another tag, for example `huette` should be replaced by `hut`. If it was replaced once it can be suggested for other users the next time. This is not yet a concern.
- Which tags to load;
    - All tags
    - Just the popular
    - Depending on the user group
    - ...

