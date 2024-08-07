amjd Alhomidi 
# markdown-toc [![NPM version](https://img.shields.io/npm/v/markdown-toc.svg?style=flat)](https://www.npmjs.com/package/markdown-toc) [![NPM monthly downloads](https://img.shields.io/npm/dm/markdown-toc.svg?style=flat)](https://npmjs.org/package/markdown-toc) [![NPM total downloads](https://img.shields.io/npm/dt/markdown-toc.svg?style=flat)](https://npmjs.org/package/markdown-toc)

> Generate a markdown TOC (table of contents) with Remarkable.

Please consider following this project's author, [Jon Schlinkert](https://github.com/jonschlinkert), and consider starring the project to show your :heart: and support.

- [Highlights](#highlights)
- [Usage](#usage)
- [API](#api)
  * [toc.plugin](#tocplugin)
  * [toc.json](#tocjson)
  * [toc.insert](#tocinsert)
  * [Utility functions](#utility-functions)
- [Options](#options)
  * [options.append](#optionsappend)
  * [options.filter](#optionsfilter)
  * [options.slugify](#optionsslugify)
  * [options.bullets](#optionsbullets)
  * [options.maxdepth](#optionsmaxdepth)
  * [options.firsth1](#optionsfirsth1)
  * [options.stripHeadingTags](#optionsstripheadingtags)
- [About](#about)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_

## Install

Install with [npm](https://www.npmjs.com/):

```sh
$ npm install --save markdown-toc
```

# Sponsors

Thanks to the following companies, organizations, and individuals for supporting the ongoing maintenance and development of markdown-toc! [Become a Sponsor](https://github.com/sponsors/jonschlinkert) to add your logo to this README, or any of [my other projects](https://github.com/jonschlinkert?tab=repositories&q=&type=&language=&sort=stargazers)

## Gold Sponsors

| [<img src="https://github.com/jonschlinkert/clone-deep/assets/383994/98036489-2cae-48a2-8d29-7dec58ea05c4" alt="https://jaake.tech/" width="100"/>](https://jaake.tech/) |
|:---:|
| [https://jaake.tech/](https://jaake.tech/) |

<br />

## Quick Start

Assuming you want to add a TOC to README.md:

1. `$ npm install -g markdown-toc`
2. Edit README.md and insert the following line where you want the TOC inserted:<br />`<!-- toc -->`
3. `$ markdown-toc -i README.md`

## CLI

```
Usage: markdown-toc [options] <input>

  input:        The Markdown file to parse for table of contents,
                or "-" to read from stdin.

  -i:           Edit the <input> file directly, injecting the TOC at - [Highlights](#highlights)
- [Usage](#usage)
- [API](#api)
  * [toc.plugin](#tocplugin)
  * [toc.json](#tocjson)
  * [toc.insert](#tocinsert)
  * [Utility functions](#utility-functions)
- [Options](#options)
  * [options.append](#optionsappend)
  * [options.filter](#optionsfilter)
  * [options.slugify](#optionsslugify)
  * [options.bullets](#optionsbullets)
  * [options.maxdepth](#optionsmaxdepth)
  * [options.firsth1](#optionsfirsth1)
  * [options.stripHeadingTags](#optionsstripheadingtags)
- [About](#about)

_(TOC generated by [verb](https://github.com/verbose/verb) using [markdown-toc](https://github.com/jonschlinkert/markdown-toc))_;
                (Without this flag, the default is to print the TOC to stdout.)

  --json:       Print the TOC in JSON format

  --append:     Append a string to the end of the TOC

  --bullets:    Bullets to use for items in the generated TOC
                (Supports multiple bullets: --bullets "*" --bullets "-" --bullets "+")
                (Default is "*".)

  --maxdepth:   Use headings whose depth is at most maxdepth
                (Default is 6.)

  --no-firsth1: Include the first h1-level heading in a file

  --no-stripHeadingTags: Do not strip extraneous HTML tags from heading
                         text before slugifying

  --indent:     Provide the indentation to use - defaults to '  '
                (to specify a tab, use the bash-escaped $'\t')
```

## Highlights

**Features**

* Can optionally be used as a [remarkable](https://github.com/jonschlinkert/remarkable) plugin
* Returns an object with the rendered TOC (on `content`), as well as a `json` property with the raw TOC object, so you can generate your own TOC using templates or however you want
* Works with [repeated headings](https://gist.github.com/jonschlinkert/ac5d8122bfaaa394f896)
* Uses sane defaults, so no customization is necessary, but you can if you need to.
* [filter](#filter-headings) out headings you don't want
* [Improve](#titleize) the headings you do want
* Use a custom [slugify](#optionsslugify) function to change how links are created

**Safe!**

* Won't mangle markdown in code examples in gfm code blocks that other TOC generators mistake as being actual headings (this happens when markdown headings are show in _examples_, meaning they arent' actually headings that should be in the toc. Also happens with yaml and coffee-script comments, or any comments that use `#`)
* Won't mangle front-matter, or mistake front-matter properties for headings like other TOC generators

## Usage

```js
var toc = require('markdown-toc');

toc('# One\n\n# Two').content;
// Results in:
// - [One](#one)
// - [Two](#two)
```

To allow customization of the output, an object is returned with the following properties:

* `content` **{String}**: The generated table of contents. Unless you want to customize rendering, this is all you need.
* `highest` **{Number}**: The highest level heading found. This is used to adjust indentation.
* `tokens` **{Array}**: Headings tokens that can be used for custom rendering

## API

### toc.plugin

Use as a [remarkable](https://github.com/jonschlinkert/remarkable) plugin.

```js
var Remarkable = require('remarkable');
var toc = require('markdown-toc');

function render(str, options) {
  return new Remarkable()
    .use(toc.plugin(options)) // <= register the plugin
    .render(str);
}
```

**Usage example**

```js
var results = render('# AAA\n# BBB\n# CCC\nfoo\nbar\nbaz');
```

Results in:

```
- [AAA](#aaa)
- [BBB](#bbb)
- [CCC](#ccc)
```

### toc.json

Object for creating a custom TOC.

```js
toc('# AAA\n## BBB\n### CCC\nfoo').json;

// results in
[ { content: 'AAA', slug: 'aaa', lvl: 1 },
  { content: 'BBB', slug: 'bbb', lvl: 2 },
  { content: 'CCC', slug: 'ccc', lvl: 3 } ]
```

### toc.insert

Insert a table of contents immediately after an _opening_ `<!-- toc -->` code comment, or replace an existing TOC if both an _opening_ comment and a _closing_ comment (`<!-- tocstop -->`) are found.

_(This strategy works well since code comments in markdown are hidden when viewed as HTML, like when viewing a README on GitHub README for example)._

**Example**

```
<!-- toc -->
- old toc 1
- old toc 2
- old toc 3
<!-- tocstop -->

## abc
This is a b c.

## xyz
This is x y z.
```

Would result in something like:

```
<!-- toc -->
- [abc](#abc)
- [xyz](#xyz)
<!-- tocstop -->

## abc
This is a b c.

## xyz
This is x y z.
```

### Utility functions

As a convenience to folks who wants to create a custom TOC, markdown-toc's internal utility methods are exposed:

```js
var toc = require('markdown-toc');
```

* `toc.bullets()`: render a bullet list from an array of tokens
* `toc.linkify()`: linking a heading `content` string
* `toc.slugify()`: slugify a heading `content` string
* `toc.strip()`: strip words or characters from a heading `content` string

**Example**

```js
var result = toc('# AAA\n## BBB\n### CCC\nfoo');
var str = '';

result.json.forEach(function(heading) {
  str += toc.linkify(heading.content);
});
```

## Options

### options.append

Append a string to the end of the TOC.

```js
toc(str, {append: '\n_(TOC generated by Verb)_'});
```

### options.filter

Type: `Function`

Default: `undefined`

Params:

* `str` **{String}** the actual heading string
* `ele` **{Objecct}** object of heading tokens
* `arr` **{Array}** all of the headings objects

**Example**

From time to time, we might get junk like this in our TOC.

```
[.aaa([foo], ...) another bad heading](#-aaa--foo--------another-bad-heading)
```

Unless you like that kind of thing, you might want to filter these bad headings out.

```js
function removeJunk(str, ele, arr) {
  return str.indexOf('...') === -1;
}

var result = toc(str, {filter: removeJunk});
//=> beautiful TOC
```

### options.slugify

Type: `Function`

Default: Basic non-word character replacement.

**Example**

```js
var str = toc('# Some Article', {slugify: require('uslug')});
```

### options.bullets

Type: `String|Array`

Default: `*`

The bullet to use for each item in the generated TOC. If passed as an array (`['*', '-', '+']`), the bullet point strings will be used based on the header depth.

### options.maxdepth

Type: `Number`

Default: `6`

Use headings whose depth is at most maxdepth.

### options.firsth1

Type: `Boolean`

Default: `true`

Exclude the first h1-level heading in a file. For example, this prevents the first heading in a README from showing up in the TOC.

### options.stripHeadingTags

Type: `Boolean`

Default: `true`

Strip extraneous HTML tags from heading text before slugifying. This is similar to GitHub markdown behavior.

## About

<details>
<summary><strong>Contributing</strong></summary>

Pull requests and stars are always welcome. For bugs and feature requests, [please create an issue](../../issues/new).

</details>

<details>
<summary><strong>Running Tests</strong></summary>

Running and reviewing unit tests is a great way to get familiarized with a library and its API. You can install dependencies and run tests with the following command:

```sh
$ npm install && npm test
```

</details>

<details>
<summary><strong>Building docs</strong></summary>

_(This project's readme.md is generated by [verb](https://github.com/verbose/verb-generate-readme), please don't edit the readme directly. Any changes to the readme must be made in the [.verb.md](.verb.md) readme template.)_

To generate the readme, run the following command:

```sh
$ npm install -g verbose/verb#dev verb-generate-readme && verb
```

</details>

### Related projects

You might also be interested in these projects:

* [gfm-code-blocks](https://www.npmjs.com/package/gfm-code-blocks): Extract gfm (GitHub Flavored Markdown) fenced code blocks from a string. | [homepage](https://github.com/jonschlinkert/gfm-code-blocks "Extract gfm (GitHub Flavored Markdown) fenced code blocks from a string.")
* [markdown-link](https://www.npmjs.com/package/markdown-link): Micro util for generating a single markdown link. | [homepage](https://github.com/jonschlinkert/markdown-link "Micro util for generating a single markdown link.")
* [markdown-utils](https://www.npmjs.com/package/markdown-utils): Tiny helpers for creating consistenly-formatted markdown snippets. | [homepage](https://github.com/jonschlinkert/markdown-utils "Tiny helpers for creating consistenly-formatted markdown snippets.")
* [pretty-remarkable](https://www.npmjs.com/package/pretty-remarkable): Plugin for prettifying markdown with Remarkable using custom renderer rules. | [homepage](https://github.com/jonschlinkert/pretty-remarkable "Plugin for prettifying markdown with Remarkable using custom renderer rules.")
* [remarkable](https://www.npmjs.com/package/remarkable): Markdown parser, done right. 100% Commonmark support, extensions, syntax plugins, high speed - all in… [more](https://github.com/jonschlinkert/remarkable) | [homepage](https://github.com/jonschlinkert/remarkable "Markdown parser, done right. 100% Commonmark support, extensions, syntax plugins, high speed - all in one.")

### Contributors

| **Commits** | **Contributor** |
| --- | --- |
| 199 | [jonschlinkert](https://github.com/jonschlinkert) |
| 9   | [doowb](https://github.com/doowb) |
| 4   | [dbooth-boston](https://github.com/dbooth-boston) |
| 3   | [sapegin](https://github.com/sapegin) |
| 3   | [Marsup](https://github.com/Marsup) |
| 2   | [dvcrn](https://github.com/dvcrn) |
| 2   | [maxogden](https://github.com/maxogden) |
| 2   | [twang2218](https://github.com/twang2218) |
| 2   | [zeke](https://github.com/zeke) |
| 1   | [Vortex375](https://github.com/Vortex375) |
| 1   | [chendaniely](https://github.com/chendaniely) |
| 1   | [Daniel-Mietchen](https://github.com/Daniel-Mietchen) |
| 1   | [Feder1co5oave](https://github.com/Feder1co5oave) |
| 1   | [garygreen](https://github.com/garygreen) |
| 1   | [TehShrike](https://github.com/TehShrike) |
| 1   | [citizenmatt](https://github.com/citizenmatt) |
| 1   | [mgroenhoff](https://github.com/mgroenhoff) |
| 1   | [rafaelsteil](https://github.com/rafaelsteil) |
| 1   | [RichardBradley](https://github.com/RichardBradley) |
| 1   | [sethvincent](https://github.com/sethvincent) |
| 1   | [shanehughes3](https://github.com/shanehughes3) |
| 1   | [bcho](https://github.com/bcho) |
| 1   | [lu22do](https://github.com/lu22do) |

### Author

**Jon Schlinkert**

* [GitHub Profile](https://github.com/jonschlinkert)
* [Twitter Profile](https://twitter.com/jonschlinkert)
* [LinkedIn Profile](https://linkedin.com/in/jonschlinkert)

### License

Copyright © 2023, [Jon Schlinkert](https://github.com/jonschlinkert).
Released under the [MIT License](LICENSE).

***

_This file was generated by [verb-generate-readme](https://github.com/verbose/verb-generate-readme), v0.8.0, on July 12, 2023._
