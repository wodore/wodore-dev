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
- `constructor(self,name,icon=DEFAULT_TAG_ICON,color=DEFAULT_TAG_COLOR,parent="",approved=false,counter=1)`
- `get_name()`
- `is_global()`
- `set(attribute, name)` icon, color, ...
- `set_tag(name,options)`
- `set_relatedTo(tag_names)`
- `remove_relatedTo(tag_names)`
- `set_approved(bool)`
- `is_approved()`
- `get_counter()`
- `incr_counter(incr=1)`
- `decr_counter(decr=1)`

`Tags` Methods
---------------
- `get_suggestions(tag,limit,scope)` scope is either global, group or both
- `get_tags(user_group, min_counter, max_counter, oroder)`
- `add_tags(tag_array)` is used for updates as well
- `remove_tags(tag_array)`

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
In future a third DB called `tag_approve_request_db` is probably used which saves all approve requests for a tags.

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

| tag\_key          |  related\_to      | counter   
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

