# Responsive Image Mapping

## Purpose

This is an idea that seeks to give web developers a language to describe responsive images.

The [src-N spec draft](http://tabatkins.github.io/specs/respimg/Overview.html) states 3 distinct "use-cases that the responsive images community has been attempting to address":
These are:

- Resolution-based discrimination
- Art-direction discrimination
- Variable-size resolution discrimination

This idea aims to fulfil the same use-cases.

## The Idea

The idea essentially is to delegate the responsibility for responsive images away from the &lt;img&gt; markup and into a separate syntax (Responsive Image Map, or RIM). The src attribute of the &lt;img&gt; tag is the building block used to determine what image to request from the server given defined @media evaluations.

### Clean markup, descriptive syntax

This concept follows the extraction of styling away from the HTML and into CSS: the responsive image problem seems to be comparable with the complexities of styling a HTML document: a task larger than any single or set of extra attributes can solve, or should solve (think inline styles).

In terms of semantics, arguably the &lt;img&gt; tag's meaning comes from the alt attribute more than the src attribute, and by removing any media-based detail the verbosity is minimised and semantic meaning is preserved.

The value of the src attribute becomes a media-independent representation of the image, a default (mobile-first, mobile-last, etc.) version and a fallback for older browsers.

### Possible syntax

The &lt;img&gt; element remains identical:

	<img src="cream-tea.jpg" />
	<img src="my-profile-pic.jpg" class="profile-pic" />

...etc.

The RIM file (or possibly a document section, much like the &lt;style&gt; element) contains the logic that the browser must evaluate to determine what image to request from the server. Currently, the syntax is designed to be as similar to CSS as possible to aid readability and pairing with existing responsive styling techniques.

Here's a possible snippet:

	@media(min-width:40em) {

		[name='cream-tea'] {
			name: 'cream-tea_devonish';
		}

		.profile-pic {
			name: name + "_large";
		}
	}

As with CSS, the min-width refers to the document's viewport. When applying the logic of the RIM syntax, the browser first splits the image's src attribute (the "base" image) into the following parts:

- src (the entire src attribute)
- **scheme** (e.g. "https:")
- **provider** (e.g. "//github.com/")
- **path()** (a 1-index array)
- **pathDelimiter** (the delimiter used to split the path)
- **name** (e.g. "cream-tea")
- **extension** (e.g. ".jpg")
- **params()**	(the query string parameter dictionary)
- **paramsDelimiter** (the delimiter used to split the parameter list)

These parts are then all available to be changed as part of the RIM file:

	provider: "//github.io/";
	name: "another-image";
	extension: ".gif";

### Flexibility of use

The idea is to allow developers to create their own workflows and conventions.

- The images are in folders called small, medium, large: manipulate the path() array
- The images are named "_s", "_m", "_l": manipulate the image name

...Hopefully you get the idea.