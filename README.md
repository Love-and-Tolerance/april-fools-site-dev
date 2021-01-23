# Official Love & Tolercane Resource Pack site source code

This is the source code of the site. The site is built with [Gulp](https://github.com/gulpjs/gulp) using [Pug](https://github.com/pugjs/pug), [Sass](https://github.com/sass/sass) and [TypeScript](https://github.com/microsoft/typescript).

## Prerequisites

- Unix-based OS, e.g. Ubuntu (recommended but not required)
- Node.js v14.x or v15.x
- pnpm v5.14.3 or later

## Development

### Installing Node

[Node.js](http://nodejs.org/) is required to develop this site. Go to [downloads](https://nodejs.org/en/download/current/) page and download binaries for your OS (you can use installer, but this guide doesn't cover it. Though, it should do all the process automatically).

Extract `node` and `npm` binaries to your binaries folder. If you haven't created it yet you may want to add it's path to environment variable `PATH`. Also add this path to `PATH`: `./node_modules/.bin`.

Check the setup by typing `node -v` and `npm -v`. This should print versions of installed binaries (check Prerequisites).

[npm team installation guide](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

Once done, run `npm i -g pnpm` to install pnpm. Type `pnpm -v` to verify it worked.

### NPM permission issues

It is recommended to install global modules to a directory which doesn't require superuser to write. To make modules be installed in that folder automatically run this in your terminal: `npm config set prefix "/home/PUT_USERNAME_HERE/.npm-global"` (you can use different folder).
NPM shouldn't have this issue on Windows.

[npm team guide on this issue](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

### Cloning the repo

Open terminal, go to your projects folder and clone this repo with `git clone git clone https://github.com/Love-and-Tolercane/love-tolercane-site-dev.git`. Go to the downloaded folder and type `pnpm i` to install dependencies.

Now you are ready to run the site! Type `pnpm run dev` to start dev server or `pnpm run build` to build the site to `dist` folder.

## Data files

Data files are written in [JSON5 format](https://json5.org/). They are usually used in Pug templates. You can change the files while dev server is running and after a change the site will be automatically rebuilt.

The data is accessed via `data` Pug local. The `data` object respects ierarchy structure of files. Some files have their own global definition.

Also there can be files and folders with the same name (e.g. `cards.json` and `cards/` folder). They are just merged in one object (e.g. `data.cards` will have properties of `cards.json` and files from `cards/` folder).

### Site information

Accessed via `site` Pug local.

```json5
{
    // Title pattern for pages. Can be seen in tab name
    "title": "%s - Love & Tolercane",
    // Site name. Can be seen in preview embeds like those ones when you share a link in Discord
    "name": "Love & Tolercane Official site",
    // Canonical URL. For OpenGraph
    "url": "https://love-tolercane.com/",
    // Site description. Used for embeds if a page doesn't provide it's own description
    "description": "Hello, and welcome to the home of Love and Tolercane, a Twilicane-inspired resource pack!",
    // Links to use in templates globally
    "links": {
        // Main GitHub repo of the resource pack
        "githubRepo": "https://github.com/Love-and-Tolercane/Love-and-Tolercane",
        // Google archive of old versions of the resource pack
        "googleArchive": "https://drive.google.com/drive/folders/1Tz9zB1VWenbSsVrwjBYRs4gwy28sHU5P?usp=sharing",
        // The Discord invite
        "discordInvite": "https://discord.gg/fxNMGvm"
    }
}
```

### Site menu

Use `getMenuList()` to get an array of entry names. Use `getMenuEntry(name)` to get the path of menu entry (must be used for dev server support)

```json5
{
    // Paths must end with /
    "Home": "/",
    "Page 1": "/path/to/page/1/",
    "Page 2": "/path/to/page/2/"
}
```

### Cards

Cards are accessed via `data.cards`.

#### Regular cards

Pass these cards to `card` or `cards` Pug mixins which are available on every page that extends main layout:

```pug
+card(data.cards.features[0])
+cards(data.cards.features)

//- Pass an extra boolean pararameter `link` which turns
//- the card into a link (example on Downloads page)
+card(data.cards.features[0], true)
+cards(data.cards.features, true)
```

```json5
[
    {
        "figure": "namespace/filename.ext",
        "title": "Example card",
        "content": "Content of the card. **Markdown supported.**",
        "url": "https://example.com/optional/link"
    }
]
```

#### Contact cards

Pass these cards to `contact-card` or `contact-cards` Pug mixins which are available in `src/templates/partials/cards/contact.pug`:

```pug
+contact-card(data.cards.team[0])
+contact-cards(data.cards.team)
```

```json5
[
    {
        "name": "Member name",
        "description": "Member role",
        // Optional
        "nickname": "Member Minecraft nickname",
        // src/assets/cards/${path}
        "avatar": "team/avatar.ext",
        // Social links. Optional
        "social": {
            "discord": "Twi#6225",
            "email": "username@example.com",
            "github": "username",
            "twitter": "username",
            "vk": "username"
        }
    },
]
```

## Pug templates

### Layout

Pages of this site use a layout which is defined in `src/templates/layout.pug` file. A pages that uses it should look like this:

```pug
//- Instruct Pug compiler to extend our layout
extends ../../layout.pug

//- Include some needed mixins
include ../../partials/something.pug

//- Here are the settings of the page
//- All settings are optional as the block itself
block settings
    //- Page title which will show up in tab title of a browser
    - var title = "Tab title";
    //- Menu entry name if you want to highlight
    - var menuEntry = "Menu entry";
    //- Description which will show up in embeds like Discord one when you share a link
    - var description = "Some description";
    //- CSS file name without extension to load for this page.
    //- The file must be stored in `src/styles/pages/filename.scss`
    - var css = "some-styles";
    //- Same as `css` but for scripts
    //- Scripts are stored in `src/scripts/filename.ts` and written in TypeScript
    - var js = "some-scripts";

//- The content of the page
block content
    h1 Hello, world!

```

### Markdown

Pug config provides a filter and a function to render markdown. Main layout also provides a mixin.

Markdown is rendered by [`markdown-it`](https://www.npmjs.com/package/markdown-it) which supports plugins. Active plugins:

- [`markdown-it-attrs`](https://www.npmjs.com/package/markdown-it-attrs) - lets you add attributes to rendered HTML elements
- [`markdown-it-anchor`](https://www.npmjs.com/package/markdown-it-anchor) - auto generates anchors for headers
- [`markdown-it-toc-done-right`](https://www.npmjs.com/package/markdown-it-toc-done-right) - lets you add a table of content (e.g. `[[toc]]`)

```pug
extends ../../layout.pug

block content
    //- Mixin for rendering markdown blocks
    //- Useful to create a header with anchor
    +md("## I have an anchor")

    //- Render markdown inline
    //- Useful to create some generated info which is defined in json files to support some syntax
    p!= markdown("See extra information [here](/extra/)")

    //- Include a markdown file and render it to HTML
    include:markdown ./info.md
```
