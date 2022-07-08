---
obsidianUIMode: preview
---

Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

Uses __tejs_adventurecrafter_data__ shortcut-file (required).
This requires that __tejs_lists__ comes before __tejs_adventurecrafter__ in the shortcut-list.
This shortcut-file contains the major tables used by this system.  It is not publicly available for copyright reasons.  Information on how to get it is available [here](https://github.com/jon-heard/obsidian-text-expander-js_shortcutFileLibrary/blob/shortcutAboutString/docs/adventurecrafter_dataSetup.txt).

Uses __tejs_lists__ shortcut-file (required).
This requires that __tejs_lists__ comes before __tejs_adventurecrafter__ in the shortcut-list.
Adds and uses lists for plotlines and characters.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the theme order and final theme switch state.

Uses __tejs_mythicv2__ shortcut file (optional).
The plotlines and characters lists are created to reference the __tejs_mythicv2__ lists.
This requires that __tejs_mythicv2__ comes before __tejs_adventurecrafter__ in the shortcut-list.


~~
```
^tejs setup$
```
~~
```js
window._tejs ||= {};

if (!window._tejs.adventurecrafter?.themes ||
	!window._tejs.adventurecrafter?.character_identities ||
	!window._tejs.adventurecrafter?.character_descriptors ||
	!window._tejs.adventurecrafter?.character_specialTraits ||
	!window._tejs.adventurecrafter?.plot_meta ||
	!window._tejs.adventurecrafter?.plot)
{
	console.log("Adventurecrafter table data not found.\nAdventurecrafter shortcuts disabled.");
	window._tejs.adventurecrafter ||= {};
	window._tejs.adventurecrafter.disabled = true;
}
else
{
	delete window._tejs.adventurecrafter.disabled;
}


window._tejs.state ||= {};
window._tejs.state.adventurecrafter ||= {};
window._tejs.state.adventurecrafter.themes ||= [];
window._tejs.state.lists ||= {};

if (!window._tejs.state.lists.plotlines)
{
	if (!window._tejs.state.lists.threads)
	{
		window._tejs.state.lists.plotlines ||= { type: "basic", content: [] };
		delete window._tejs.state.lists.plotline_dupes;
	}
	else
	{
		window._tejs.state.lists.plotline_dupes ||= { type: "basic", content: [] };
		window._tejs.state.lists.plotlines ||=
			{ type: "combo", content: ["threads", "plotline_dupes"] };
	}
}

if (!window._tejs.state.lists.characters)
{
	if (!window._tejs.state.lists.pcs || !window._tejs.state.lists.npcs)
	{
		window._tejs.state.lists.characters ||= { type: "basic", content: [] };
		delete window._tejs.state.lists.character_dupes;
	}
	else
	{
		window._tejs.state.lists.character_dupes ||= { type: "basic", content: [] };
		window._tejs.state.lists.characters ||=
			{ type: "combo", content: ["pcs", "npcs", "character_dupes"] };
	}
}

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.adventurecrafter ||= expand =>
{
	expand("reset adventurecrafter");
};

```
~~
Sets up a state variable for adventurecrafter.  Sets up callback for state "reset" event to reset itself.


~~
```
^tejs shutdown$
```
~~
```js
delete window._tejs.listeners?.state?.onReset?.adventurecrafter;
```
~~
Unregisters event callbacks.


~~
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
function aRemoveDuplicates(a) { return [...new Set(a)]; }
function isAdventurecrafterDisabled()
{
	if (window._tejs.adventurecrafter.disabled)
	{
		return "ADVENTURECRAFTER SHORTCUTS DISABLED - missing table data.\nIf __tejs_adventurecrafter_data__ exists, make sure it is listed _before_ __tejs_adventurecrafter__ in the shortcut-file list.\n\n";
	}
	return null;
}
```
~~
Some useful functions


~~
```
^reset adventurecrafter$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.adventurecrafter ||= {};
window._tejs.state.adventurecrafter.themes = [];
window._tejs.state.lists ||= {};

if (!window._tejs.state.lists.threads)
{
	window._tejs.state.lists.plotlines = { type: "basic", content: [] };
	delete window._tejs.state.lists.plotline_dupes;
}
else
{
	window._tejs.state.lists.plotline_dupes = { type: "basic", content: [] };
	window._tejs.state.lists.plotlines =
		{ type: "combo", content: ["threads", "plotline_dupes"] };
}

if (!window._tejs.state.lists.pcs || !window._tejs.state.lists.npcs)
{
	window._tejs.state.lists.characters = { type: "basic", content: [] };
	delete window._tejs.state.lists.character_dupes;
}
else
{
	window._tejs.state.lists.character_dupes = { type: "basic", content: [] };
	window._tejs.state.lists.characters =
		{ type: "combo", content: ["pcs", "npcs", "character_dupes"] };
}

return "Adventure crafter reset.";
```
~~
reset adventurecrafter - Reset adventurecrafter state to defaults.
***


~~
```
^turning point$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
if (expand("themes pick") < 4)
{
	return "Turning point not generated.  Not all theme slots filled.\n\n";
}
let result = "__Turning point__:\n";
for (let i = 0; i < 5; i++)
{
	result += "- " + expand("plot point")[1] + "\n";
}
return result + "\n";
```
~~
turning point - Get a list of five plot points to represent a major milestone in a plotline.


~~
```
^plot point$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
const theme = expand("themes pick");
if (theme.length < 4)
{
	return "Plot point not generated.  Not all theme slots filled.\n\n";
}
let result = ["__Plot point__:\n"];
const plotPoint = aPickWeight(window._tejs.adventurecrafter.plot, theme[3]);
if (!plotPoint[7])
{
	result.push(plotPoint[0] + "\n    " + plotPoint[6]);
}
else
{
	if (plotPoint[7] == 1)
	{
		result.push(plotPoint[0]);
	}
	else if (plotPoint[7] == 2)
	{
		const metaPoint = aPickWeight(window._tejs.adventurecrafter.plot_meta);
		result.push(metaPoint[0] + "\n    " + metaPoint[2]);
	}
}
result.push("\n\n");
return result;
```
~~
plot point - Get a single plot point, generated from the plot points table.
***


~~
```
^themes?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
let result = "__Current theme set__:\n";
for (let i = 0; i < 5; i++)
{
	result += (i+1) + ". " +
		(window._tejs.adventurecrafter.themes[
			window._tejs.state.adventurecrafter.themes[i] ?? 9] || "") +
		"\n"
}
return result + "__Theme options__:\n   1 - Action    2 - Tension    3 - Mystery    4 - Social    5 - Personal\n\n";
```
~~
themes - Show the ordered list of themes.


~~
```
^themes? fill$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
let result = "Rolling remaining themes...\n";
while (window._tejs.state.adventurecrafter.themes.length < 5)
{
	result += "- " + expand("themes add")[0] + "\n";
}
return result + "...done\n\n";
```
~~
themes fill - Fills the remaining open theme slots with random themes.


~~
```
^themes? add$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
let r = roll(5);
while (window._tejs.state.adventurecrafter.themes.contains(r-1))
{
	r = roll(5);
}
return expand("themes add " + r);
```
~~
themes add - Fills the next open theme slot with a random theme.


~~
```
^themes? add ([1-5])$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
if (window._tejs.state.adventurecrafter.themes.length >= 5)
{
	return "No theme added.  All five theme slots are already filled.\n\n";
}
$1 = Number($1);
window._tejs.state.adventurecrafter.themes.push($1-1);
return [ "Theme slot " + window._tejs.state.adventurecrafter.themes.length + " set to " + window._tejs.adventurecrafter.themes[$1-1], "\n\n" ];
```
~~
themes add {theme id} - Fills the next open theme slot with the theme of {theme id} (a required integer from 1 to 5).  {theme id} can be one of these options:
    1 - Action    2 - Tension    3 - Mystery    4 - Social    5 - Personal


~~
```
^themes? reset$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
window._tejs.state.adventurecrafter.themes = [];
return "All theme slots cleared.\n\n";
```
~~
themes reset - Clear all theme slots.


~~
```
^themes? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
if (window._tejs.state.adventurecrafter.themes.length != 5)
{
	return [ "Theme not picked.  Not all theme slots filled.\n\n" ];
}
let pick = roll(10);
if      (pick <  5) { pick = 0; }
else if (pick <  8) { pick = 1; }
else if (pick < 10) { pick = 2; }
else
{
	pick = (window._tejs.state.priorPickWas3rd ? 4 : 3);
	window._tejs.state.priorPickWas3rd = !window._tejs.state.priorPickWas3rd;
}
pick = window._tejs.state.adventurecrafter.themes[pick];
return [ "Theme ", window._tejs.adventurecrafter.themes[pick], " (", (pick+1), ") picked.\n\n" ];
```
~~
themes pick - Pick a weighted random theme, as per the Adventure Crafter rules.
***


~~
```
^ac chars? gen$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

let special = aPickWeight(window._tejs.adventurecrafter?.character_specialTraits, 1);
special = "- __Special trait__ - " + special[0] + "\n    " + special[2] + "\n";

let identity = aPickWeight(window._tejs.adventurecrafter?.character_identities, 1);
if (identity[2])
{
	identity = [];
	for (let i = 0; i < 2; i++)
	{
		let subIdentity = [];
		do
		{
			subIdentity =
				aPickWeight(window._tejs.adventurecrafter?.character_identities, 1);
		}
		while (subIdentity[2]);
		identity.push(subIdentity[0]);
	}
}
else
{
	identity = [ identity[0] ];
}
identity = "- __Identity__ - " + identity.join(", ") + "\n";

let descriptor = aPickWeight(window._tejs.adventurecrafter?.character_descriptors, 1);
if (descriptor[2])
{
	descriptor = [];
	for (let i = 0; i < 2; i++)
	{
		let subDescriptor = [];
		do
		{
			subDescriptor =
				aPickWeight(window._tejs.adventurecrafter?.character_descriptors, 1);
		}
		while (subDescriptor[2]);
		descriptor.push(subDescriptor[0]);
	}
}
else
{
	descriptor = [ descriptor[0] ];
}
descriptor = "- __Descriptor__ - " + descriptor.join(", ") + "\n";

return "__Character__:\n" + special + identity + descriptor + "\n";
```
~~
ac chars gen - Generate a new character description, as per the Adventure Crafter rules.
***


~~
```
^ac chars?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
let list = aRemoveDuplicates(expand("lists listraw characters")).join(", ") || "NONE";
return "__Characters__:\n" + list + "\n\n";
```
~~
ac chars - List the character items.


~~
```
^ac chars? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
const r = roll(25);
let result = expand("lists pick characters " + r)[1];
if (result)
{
	result = [ "__Character picked__:\n", result ];
}
if (!result)
{
	if (r < 13)
	{
		result = r%4 ? [ "Create a new character" ] : null;
	}
	else
	{
		result = (r-1)%4 ? null : [ "Create a new character" ];
	}
}
if (!result)
{
	let list = aRemoveDuplicates(expand("lists listraw characters"));
	result = [ "__Pick the most logical character__:\n" + list.join(", ") ];
}
result.push("\n\n");
return result;
```
~~
ac chars pick - Pick a random char, as per the Adventure Crafter rules.


~~
```
^ac chars? add (.*)$
```
~~
```js
if (expand("lists type characters") == "basic")
{
	expand("lists add characters " + $1);
}
else
{
	expand("lists add character_dupes " + $1);
}
return "__" + list[$1-1] + "__ added to characters.\n\n";

```
~~
ac chars add {character name} - Add the given {character name} (required text) to the list of characters.

~~
```
^ac chars? dupe$
```
~~
```js
	if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
	const list = expand("lists listraw characters");
	let result = "__Pick a character to duplicate.  Enter the shortcut again with the index__:\n";
	for (let i = 0; i < list.length; i++)
	{
		result += (i+1) + ". " + list[i] + "\n";
	}
	return result + "\n";
```
~~
ac chars dupe - Show indices of character list entries for duplication.


~~
```
^ac chars? dupe ([1-9][0-9]*)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
const list = expand("lists listraw characters");
$1 = Number($1);
if ($1 > list.length)
{
	return "Character not duplicated.  Invalid index given (" + $1 + ").\n\n";
}
if (expand("lists type characters") == "basic")
{
	expand("lists add characters " + list[$1-1]);
}
else
{
	expand("lists add character_dupes " + list[$1-1]);
}
return "Character entry __" + list[$1-1] + "__ duplicated.\n\n";
```
~~
ac chars dupe {character index} - Create a new character list entry that is the same as the character list entry with the index of {character index} (a required, positive integer).
***


~~
```
^ac plots?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
let list = aRemoveDuplicates(expand("lists listraw plotlines")).join(", ") || "NONE";
return "__Plotlines__:\n" +	list + "\n\n";
```
~~
ac plots - List the plotline items.


~~
```
^ac plots? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
const r = roll(25);
let result = expand("lists pick plotlines " + r)[1];
if (result)
{
	result = [ "__Plotline picked__:\n", result ];
}
if (!result)
{
	result = (r+2)%4 ? null : [ "Create a new plotline" ];
}
if (!result)
{
	let list = aRemoveDuplicates(expand("lists listraw plotlines"));
	result = [ "__Pick the most logical plotline__:\n" + list.join(", ") ];

}
result.push("\n\n");
return result;
```
~~
ac plots pick - Pick a random plotline, as per the Adventure Crafter rules.


~~
```
^ac plots? add (.*)$
```
~~
```js
if (expand("lists type plotlines") == "basic")
{
	expand("lists add plotlines " + $1);
}
else
{
	expand("lists add plotline_dupes " + $1);
}
return "__" + list[$1-1] + "__ added to plotlines.\n\n";

```
~~
ac chars add {character name} - Add the given {character name} (required text) to the list of characters.


~~
```
^ac plots? dupe$
```
~~
```js
	if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
	const list = expand("lists listraw plotlines");
	let result = "__Pick a plotline to duplicate.  Enter the shortcut again with the index__:\n";
	for (let i = 0; i < list.length; i++)
	{
		result += (i+1) + ". " + list[i] + "\n";
	}
	return result + "\n";
```
~~
ac plots dupe - Show indicies plotline list entries for duplication.


~~
```
^ac plots? dupe ([1-9][0-9]*)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }
const list = expand("lists listraw plotlines");
$1 = Number($1);
if ($1 > list.length)
{
	return "Plotline not duplicated.  Invalid index given (" + $1 + ").";
}
if (expand("lists type plotlines") == "basic")
{
	expand("lists add plotlines " + list[$1-1]);
}
else
{
	expand("lists add plotline_dupes " + list[$1-1]);
}
return "Plotline entry __" + list[$1-1] + "__ duplicated.\n\n";
```
~~
ac plots dupe {plotline index} - Create a new plotline list entry that is the same as the plotline list entry with the index of {plotline index} (a required, positive integer).