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
    "key"        : "TagName",
    "parent"    : "UserGroupKey", // if it is a usergroup tag it needs a parent
    "options"   : {               // tag options
      "color"    : "hex color code",
      "icon"     : "icon name",
      "icon-type" : "font/svg/png"
    },
    "stats" : {             // tag statistics
      "cnt"       : 0, // Tag counter (how oftern is it used)
      "relatedTo" : [{"id":"TagName", "cnt":1},...],  // Which tags are related and how often.
                                                      // This is used to give tag suggestions.
                                                      // This is updated on the global level 
                                                      // as well as local (user group level)
      "replaceBy" : "replaceBy_key",  // A key to a replaceBy object ...
      }
    "approved"  : false,        // The tag is approbed for public tags
    "approveRequest" : false,
  }
}
```

Example
----------
The working user group is called *EasterTour* which has the key `xyz`.
The following three points are added:
- *Bus station*, tags: `bus, public transport, over 2000, break`
- *Peak 4*,tags: `peak, over 2000`
- *Sleep Good*, tags: `accomodation, tent, over 2000, break`

Beacuse all the tags are used the first time they are added to the global tag list:
`bus, public transport, break, peak, over 2000, accomodation, tent`. `over 2000` and `break` was used twice, but only once in the global tag list. Let's check the tag `break` (simplified):

```javascript
{ "tag": {
    "id"        : "break",
    "parent"    : "", // global (no parent)
    "stats" : {
      "cnt"       : 2, // Tag counter (how oftern is it used)
      "relatedTo" : [{"id":"bus", "cnt":1},
                     {"id":"public transport", "cnt":1},
                     {"id":"accomodation", "cnt":1},
                     {"id":"tent", "cnt":1},
                     {"id":"over 2000", "cnt":2}], // counter increased
      }
  }
}
```
The same tag is saved for the user group with `"parent" : "xyz"`. 

If the same tags are used in another user group the gobal `relatedTo` and `cnt` would be updated, but not the tags with the usergroup *EasterTour*.

Methods
--------
Every interaction with the tag model is done by methods. The basic methods are:
- `get_name()`
- `is_global()`
- `set(attribute, name)`
- `set_tag(name,options)`
- `set_relatedTo(tag_names)`
- `remove_relatedTo(tag_names)`
- `set_approved(bool)`
- `is_apprived()`
- `get_counter()`
- `incr_counter(incr=1)`
- `decr_counter(decr=1)`

Class methods:
- `get_suggestions(tag,limit,scope)` scope is either global, group or both
- `get_tags(user_group, min_counter, max_counter, oroder)`
- `add_tags(tag_array)` is used for updates as well
- `remove_tags(tag_array)`

Concerns
---------
- Different groups with different icons (for the same tag). If this happens the icon of the current *active* group will be used.
- Different languages and tags in general which mean the same. For example `hut` and `huette` or even `hütte`. If the tag is only used for one user group it is no problem, but for future extension it would be nice to have unique public tags. For this is the field `replaceBy`. If the tag is not  `approved` for the public it needs to be replaced by another tag, for example `huette` should be replaced by `hut`. If it was replaced once it can be suggested for other users the next time. This is not yet a concern.
- Which tags to load;
    - All tags
    - Just the popular
    - Depending on the user group
    - ...
