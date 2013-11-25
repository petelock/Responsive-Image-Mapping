# Responsive Image Mapping

## Purpose

This is an idea that seeks to give web developers a language to describe responsive images.

The proposal is **not** for an extension of CSS - but rather for a way to fully describe all solutions to image-specific responsive design problems.

The [srcN spec draft](http://tabatkins.github.io/specs/respimg/Overview.html) states 3 distinct "use-cases that the responsive images community has been attempting to address":
These are:

- Resolution-based discrimination
- Art-direction discrimination
- Variable-size resolution discrimination

This idea aims to fulfil the same use-cases.

## The Idea

The idea essentially is to delegate the responsibility for responsive images away from the `img` markup and into a separate syntax (Responsive Image Map, or RIM). The src attribute of the `img` tag is the building block used to determine what image to request from the server given defined @media evaluations.

#### Clean markup, descriptive syntax

This concept proposes that images begin to follow the path previously trodden by HTML markup - that of extracting detail away from the HTML and into CSS: Without CSS files, pages would be made up of elements with `style` tags, which renders them incredibly unmaintainable.

The responsive image problem seems to be comparable with the complexities of styling a HTML document: while the issue could be fixed via proposed attributes `srcset` and `srcN`, maintainability seems to be lost at the preference to having all the possible images listed in the markup.

#### Alt to the rescue?

In terms of semantics, arguably the `img` tag's meaning comes from the alt attribute more than the src attribute, and by removing any media-based detail the verbosity is minimised and semantic meaning is preserved.

The value of the src attribute becomes a media-independent representation of the image, a default version (mobile-first/mobile-last, depending on the strategy used within the page's styling) and a fallback for older browsers.

### Possible syntax

The `img` element remains identical:

	<img src="cream-tea.jpg" />
	<img src="my-profile-pic.jpg" class="profile-pic" />

...etc.

The RIM file (or possibly a document section, much like the `style` element) contains the logic that the browser must evaluate to determine what image to request from the server. Currently, the syntax is designed to be as similar to CSS as possible to aid readability and pairing with existing responsive styling techniques.

#### The properties

As with CSS, the min-width refers to the document's viewport. When applying the logic of the RIM syntax, the browser first splits the image's src attribute (the "base" image) into the following parts:

- **src** (the entire src attribute)
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

#### For example...

Let's take some example HTML:

	<header>
		<img src="/images/pic-of-me.jpg" class="profile-pic" />
	</header>
	...
	<article>
		<h2>The humble cream tea</h2>
		<img name="/images/cream-tea_400.jpg" />
		<p>...</p>
	</article>

Here's a possible RIM snippet:

	/* tweak image names for slightly larger screens */
	@media(min-width:640px) {

		/* The page layout means a larger image should be used for the header profile pic. */
		header .profile-pic {
			name: name + "_large";
		}

		/* Perhaps at this point our page changes from a 1 column, max 400px column
		   to a 2-column grid, with each column at 320px.
		   Use a regex function available as part of RIM to change the image name. */
		article img {
			name: replace(name, "^([A-Z0-9\-]*)_[0-9]+$" + "\1_320");
		}
	}

	/* all images are given a _2x suffix, regardless of device width */
	@media(min-device-pixel-ratio: 2) {
		extension: "_2x" + extension;
	}

### Flexibility of use

The idea is to allow developers to create their own workflows and conventions.

- If the images are in folders called small, medium, large: manipulate the `path()` array
- If the images are appended with "-small", "-medium", "-large": manipulate the image `name`

...Hopefully you get the idea.

## Disclaimer

This is simply an idea, not a completely thought through proposal! However, I am interested in feedback around the concept.

- Would a markup-separated way of describing a website's image sizing/naming strategy help the responsive image problem?
- Is the use of the `src` as a base/candidate/representative, alongside the continued use of the `alt` attribute, enough for semantic integrity?