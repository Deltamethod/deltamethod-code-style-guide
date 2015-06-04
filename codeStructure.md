# Campaign Suite code structure, achitecture and guidelines

## Basic principles and notes

Campaign Suite (CS) is a feature-rich one-page web app with 100% client-side rendering. It only fetches the initial page from
the server, then uses JSON data APIs to load the data, send and receive calls.
 
The app was designed to work across different domains: the "static files" (i.e. frontend assets) reside on one server,
 while all the data sources are on a different machine. Sources can be adjusted using `environment.js` and cross-domain
 communication support is vital for the app.
 
CS only supports modern browsers such as Chrome and Firefox. Support for Safari is technically not provided, although
the app should work fine there (given some extra vendor prefixes in CSS + some testing). IE is not supported either,
 but latest versions should work, too.
 
CS is built on top of:

### JavaScript:
 
  * jQuery
  * Require.js
  * Twitter Flight
  * jQuery UI
  * Rivets.js + Backbone (Exoskeleton fork)
  * React.js
  
### CSS

  * LESS preprocessor (ver. 2.x)
  * BEM CSS Methodology (Yandex syntax) for classes 

## Folder structure

Here the folders are listed hierarchically from the root repository folder

### /application

Here reside the files that initialize the app, load the dependency tree and make the necessary preparations for the app
 to start and function.

### /common

Modules of various kinds, namely:

  * `binders` Rivets.js binders
  * `charts` d3-based charts
  * `controllers` Middleware layers to work with CS JSON APIs; mostly legacy code as we don't use controllers often 
  * `formatters` Rivets.js formatters
  * `mixins` Twitter Flight mixins
  * `proxies` Rivets.js proxies
  * `router` App router and related config files
  * `ui` Components that are (typically) shared across several pages, see below
  * `utilities` Random utilities

### /common/ui

This is where we place all components that can be shared across pages (we call them `blocks` as in BEM methodology).
According to BEM, blocks are guaranteed to have independent and scoped CSS and a standalone semantic meaning. 

Each block gets a folder where most of its resources reside. External dependencies are resolved via `require()`.
See [Deltamethod Styleguides](./README.md) for more details about BEM files structure and guidelines.

Certain modules are CSS-only; that means, other blocks can specify them as dependencies and use their CSS. As in most
cases with BEM blocks, their states and behavior are controlled by modifiers.

Some of the CSS-only blocks are made available as common dependencies so any other block can use them
without declaring an explicit dependency. These are managed in `/common/commonCSS.js` via `require()`.

Here is a list of components with a one-line usage explanation.

   * `adtpl` Template editing controls, multi-row, with a preview and various options (from Template Details / Text Ads)
   * `authenticate` Google MCC authentication
   * `blend` CSS-only, partially hide parts of the page with a semi-opaque layer + optional throbber
   * `body` Body styles + CSS that doesn't belong to any other block (scrollbar styles, custom fonts)
   * `breadcrumbs` Simple breadcrumbs (a series of headers representing steps or navigation history)
   * `button` All styles for buttons
   * `chat` Integrates the live chat solution from http://www.livechatinc.com
   * `checkbox` Represents a checkbox group + some internal elements (labels etc.) 
   * `checkbox-action` Helper block to check/uncheck/invert a checkbox group, used internally by other blocks
   * `checkbox-control` CSS-only. Styles for every checkbox in the app (a specific block class should be used)
   * `checkbox-counter` Helper block to display the amount of selected checkboxes, used internally by other blocks.
   * `checkbox-dropdown` A dropdown control with a list of checkbox options
   * `cron-job` UI for scheduling tasks to be run based on a schedule
   * `crumb-bar` Another implementation of breadcrumbs with clickable button-like items
   * `cs` Not a block but a legacy folder where the CS logo is stored
   * `csv-params` Options for configuring CSV files import
   * `dashboard-tooltip` A tooltip for the Analytics Dashboard
   * `dropdown` Dropdown component (use instead of `<select>`)
   * `dynamicPopup` A popup (more like a tooltip) that can be positioned dynamically
   * `error` A generic (deprecated) error block that provides extra styling in forms
   * `errorReport` A set of tools to collect and send the detailed error report to be saved in CS servers, available under the Bug icon in the toolbar for selected customers
   * `fallback-distr` A bar-like graph + some data for fallback usage distribution in templates 
   * `filter-by-tbl` Provides live filtering of the table list by table names selected
   * `filter-sort` Filter a list of items using a set of predefined options + sorting modes. Used on table/template list pages.
   * `flags` Small international flags as icons + CSS
   * `foldable` A LESS mixin + reference implementation for controls that expand/collapse something. Can be used standalone or mixed into other blocks as a LESS dependency.
   * `form-controls` CSS-only. A bar at the bottom of a form that contains buttons, helper text etc. Provides unified spacing/alignnment.
   * `form-help` Hint/help labels used in CS forms
   * `getmcc` Display a list of Google MCC accounts + related actions
   * `handle` CSS-only; a handle for all things draggable
   * `head` The page header + internal blocks under `blocks`:
     * `account-switch` Switch between different CS accounts
     * `anaMsg` Display warning/error messages from the Analytics system
   * `head-help` Help menu in the page header
   * `header` Actually a **heading**. Implements thing similar to `<H1>...<H4>` in HTML but with BEM naming
   * `hide` Deprecated. CSS-only. A block that hides its content when mixed with anything else. Use local block-specific modifiers instead.
   * `ico` App icons. Also contains a deprecated `icon.css` that uses a different name (`icon` vs `ico`) and a CSS sprite
   * `importFeedSettings` Feed settings used during feed import, shared between several pages
   * `input` Styles for input fields. Not used consistently across the app.
   * `jquery-ui` CSS-only resets for jQuery UI components loaded via external plugins (as those plugins are not implemented as blocks, the CSS overrides are collected here)
   * `label` CSS-only form label used next to a form field
   * `link` CSS-only HTML links
   * `loading` AJAX "loading..." throbber
   * `mapColumns` UI for mapping table columns to imported table data
   * `msg` Component for displaying inline form errors based on a special messaging structure used by backend CS APIs. Mostly designed to automatically show/hide messages distributed over a big form, based on what the server response is during data loading/saving. In most cases, the connection between errors and specific backend responses is expressed semantically with unique error ids used by the server and expected on the client, introduced in markup as BEM modifiers and preconfigured once during the init call. Can be also controlled programmatically via its own API. 
   * `multiple` A selection controls implemented as a set of buttons. Not widely used.
   * `overview-metadata` Helpful metadata information displayed, can probably be replaced with a more generic block
   * `p` CSS-only paragraph implementation
   * `paginator` Pagination controls
   * `palette` A palette containing draggable items such as variables etc.
   * `progress` Display progress 0..100%
   * `radio` A collection of radio buttons + extra items such as labels
   * `range-datepicker` Datepicker that supports many options and date range, based on a jQuery UI implementation
   * `rec` Recommendation styles + extra dependencies for the `table` block
   * `sample` Table selection for template creation, offers specific behavior for certain table types + selection logic
   * `save-indicator` Live saving indicator in the template details page toolbar
   * `search` A search field. Not widely used, deprecated, not correctly BEM-ed
   * `search-select` Searchable select control
   * `search-table-template` Search table and template list, works together with `search` and implicitly depends on it, not BEM-ed
   * `select` Classical HTML `<select>` + extra elements and styles
   * `select-palette` Switch categories in `palette`, has implicit dependencies from `palette`
   * `sellist` A list of items displayed in a special way, used on the Edit table page and has implicit and explicit dependencies on it
   * `split` Split button component that can act as a radio button replacement
   * `static-msg` CSS-only generic message with ok/warning/success/etc. that can be used anywhere on a page, plus a reusable LESS mixin to mix its styles into other blocks. The mixins should be used to style anything that should "look like a success/warning/error message"
   * `static-popup` A CSS-only **tooltip** that can act as a small popup, hover-based, with many presentation modifiers (direction, size, themes etc.)
   * `structure` Choose templates structure to be used in CS
   * `table` CSS-only generic styles for tables (see modifiers at the end of the LESS file for custom component-specific modifiers)
   * `table-rel` Work with table relations
   * `table-type-icon` Icons for table types, implemented as reusable sprite-based LESS mixins to be used in other components. Should be converted to a simpler structure similar to that of the `ico` block
   * `tabs` Tabs that can show/hide content parts depending on which one is chosen. Support various themes including vertical, provide an API for manual control
   * `tb` "New" toolbar component that should be used on all pages. Provides an interface for placing other buttons/components/items in the page toolbar.
   * `template` Google Ad preview, React-based
   * `template-table-toolbar` Toolbar-based dropdown to create a new table based on a set of predefined types
   * `textarea` CSS-only, styles for a textarea
   * `tip` CSS-only icon that looks like a red arrow, highlights the recommended choice etc.
   * `toolbar` Legacy (deprecated!) toolbar component, currently only used on the "Excel" page as the new toolbar does not yet support reducing toolbar buttons size based on window width. Not BEM-ed properly.
   * `toolbar-select` Dropdown component for the toolbar that is supposed to replace `<select>`
   * `tooltip` Dynamic tooltip that appears on hover and follows the cursor. Used when static tooltips are not possible because of layout.
   * `tpl-list` React-based list of templates for the template overview page. A lot of magic happens in LESS with flexboxes (results are very good). Take care while editing CSS, test in Firefox, also pay attention to Firefox-specific CSS fixes at the end of the LESS file.
   * `tray` An abstract component for presenting several items for selection
   * `tree` Tree-like structure. Started as an abstract reusable tree component, but has a lot of implicit dependencies and specific features from Template Export.
   * `txt-preview` CSS-only. Display preview text for text ad templates. See usage examples for the exact HTML structure.
   * `user-settings` User settings in the toolbar
   * `var-editor` Editor for textual content in templates and other places where support for predefined variables is necessary. Based on `contenteditable` with a lot of workarounds to
   truly make certain parts of the input (tokens) non-editable. This is the most expensive component in the whole app in terms "human hours vs. number of lines". Current implementation is considered stable, all changes to the architecture should be done with extreme care. The method for rendering non-editable tokens and custom-generated canvas images seems to be the best,
   almost everything else we tried (including some Chrome-specific stuff) is worse or buggy or doesn't provide enough features. 
 
### /components

Automatically managed external FE dependencies (i.e. those that end up being part of the FE app code). Managed by Bower, do not edit/manage/change manually, see `./bower.json`
for the complete dependency list.

Files are committed to the repository, this is **intentional** and allows for faster local development + precise version control.  

### /development

Here we store the files that are used for local development. The main file is `index.html` that is used to serve the initial app page locally. Other files had some implicit connection to legacy projects, but now are not used in CS at least. 

### /manualComponents

Manually managed external dependencies, mostly projects that can't be managed via Bower, or unofficial/local forks or locally edited projects.

Manage and update with extreme caution, consult git history for possible changes.

Please avoid adding more libraries as a manual dependency unless there's a good reason for that and you know what you're doing.

## /node_modules

Local node modules used to compile FE assets. Do not stay under version control as minor differences are not that critical.

## /pages

Follows the same folder structure as `/common/ui/` but represents **pages** — independent app states that look like different pages to the user.
The internal router-based navigation in the app is only possible between pages, and each page "takes over" the control over the document,
replacing the old content, making garbage collection, disconnecting components of the previous page etc. However, the actual HTTP reload
is not happening and pages can influence global namespace, leave data and timers behind etc.

Here is the page list and some descriptions:
 
### /pages/404

Emulated 404 page that gets displayed by the router. Contains Easter eggs :) 

### /pages/analyze

CS Analytics page. Local blocks are under `blocks` folder, however, they sometimes implement part that looks "almost"
like separate pages to the user.

  * `/pages/analyze/adWordsReport` AdWords report that has been implemented for compliance with the Google policy  
  * `/pages/analyze/dashboard` Analytics dashboard
  * `/pages/analyze/dashboardMetric` Not a `block` in a strict sense, but a module that implements a graph
  * `/pages/analyze/drilldown` Drilldown page. Dig deep into stats.
  * `/pages/analyze/drilldownCache` External module to cache data, as BE requests are costly.
  * `/pages/analyze/drilldownMenu` Drilldown menu. Not implemented as a Flight component, uses a simple constructor architecture
  * `/pages/analyze/excelReports` A huge page for getting CSV reports in various formats
  * `/pages/analyze/reportsCharts` Charts functionality for reports
  * `/pages/analyze/reportsTable` Renders report data in a simple table-based view

### /pages/browserCheck

To reduce support questions, CS explicitly warns users about incompatible browsers. We know that this goes against all
 good practices. This is a business decision, and we can get rid of it if more testing can be done on platforms like IE11
 and/or Safari.

### /pages/chooseCampaignStructure

Choosing campaign structure.

### /pages/createNewTable

Creating new table. Under `blocks`, there are "virtual" pages that are not "technically" pages but appear as such to the user.

  * `copyTable` Copy existing table
  * `productFeed` Add product feed
  * `productFeedPreview` CSS-only block to style the product feed preview table
  * `regularTable` Define a new table yourself (give a name, choose column sets etc.)
  * `sampleTable` Create a table based on a predefined sample

### /pages/errorLookup

Search bugreports made by the built-in Bug Reporting. Uses different database sources for dev, qa and production.
Requires additional HTTP authentication and does not work out of the box for all users, as the data is sensitive and
can't be accessed without authorization.

### /pages/generalSettings

General application settings.

### /pages/globalVariables

Create/Edit global variables.

### /pages/history

View the history of all user actions, search, filter, browse events and changes done to various data pieces.
Although the tool is available to end users, its main purpose is to allow the consultants to check the action history
for unintended errors or mistakes. 

### /pages/mappingTool

Mapping Tool consisting of 3 steps.

### /pages/optimizationTools

A huge section of the CS app, dedicated to providing various tools and data to optimize the existing set of ads and
campaigns based on statistical analysis and recommendations made by CS Analytics.

  * `adalyzer` Adalyzer is the page that represents the results of experiments that were running in the background
  * `cleanup-tool` Clean up the ad groups that are not performing well enough
  * `keywords` Find new relevant keywords and negatives
  * `keywordsChart` Module implementing the keywords chart for the `keywords` page
  * `keywordsModel` A Flight component used as a model for `keywords`
  * `recStream` Displaying the stream of all recommendations
  
  Other pages are pretty self-descriptive:
  
  * `keywordsPanel` 
  * `recAddKeywords`
  * `recCampaignNegatives`
  * `recCampaignSettings`
  * `recChangeMatchType`
  * `recCheckUrl`
  * `recCreateTest`
  * `recDailyBudget`
  * `recDeleteKeywords`
  * `recReactivate`
  * `recRedirectTraffic`
  * `recRemoveDuplicates`

### /pages/startTour

A Welcom screen page that is displayed when a new customer logs in for the first time. It shows some helpful tips and
offers to start a Guided Tour (an assisted navigation tour around the app with hints popping up).

### /pages/synchrError

This page gets displayed when the Export process fails and the user has to be notified in order to explicitly accept
the failure and start from scratch. Displaying the page is important to make sure that users understand why the multi-step
compicated process was broken.

### /pages/syncr

Template Export process consisting of multiple steps and various states.

Subpages under `blocks` include:

  * `changes-full` Summary of changes
  * `changes-total` Summary of changes
  * `syncrTable` Display table data

### /pages/tableContent

Table Content (also Table Details or "Excel" page) is the most complicated page in the whole app. It provides table
editing functionality in an Excel-like interface with a lot of functionality, modal dialogs, features etc. hidden behind
 menu buttons or only toggled by specific actions.
 
The foundation for the Excel-like interface is the slick-grid standalone component that was forked and further extended
by Deltamethod CS Frontend Team. Special thanks goes to Mikhail Davydov for hacking the hell out of it. Note that the
original slick-grid implementation had to be patched, so you can't blindly update the code.

The page itself mostly takes care about initializing various functions of its toolbar, binding components together
 and making sure all special states are processed correctly (table being locked for editing, data import
 in progress etc.).
 
 Many of the subpages appear in modal windows, implemented with the `/common/mixins/modal/modal.js` mixin.
 
 Subpages under `blocks` are:
  
  * `confirm-deleted` Table entry deletion confirmation dialog
  * `delTable` Delete table dialog
  * `dynamicRule` Individual dynamic rule UI
  * `dynamicRules` Edit dynamic rules
  * `editTable` Edit table columns/data types and other properties
  * `emptyTable` Empty table button and dialog
  * `extractColumns` Make tables from existing table columns
  * `feedColumnSelect` Selecting a column for `extractColumns` 
  * `google-account-autofill` A component to prefill the whole table column with the same Google Account data
  * `hiddenColumns` A modal dialog to choose which columns should be hidden or visible
  * `importFeed` A multi-step modal dialog to set an external CSV feed as a data source for the existing table
  * `importTable` Import a CSV file into the existing table
  * `restoreTable` Restoring previous state of a table from backup
  * `slick-grid` An interface to the Slick Grid library that implements the "Excel-like" interface
  * `slick-grid-filter` Filter for Slick Grid
  * `slick-grid-multi-filter` Multifilter for Slick Grid
  * `tableTypeSelect` Table type selector for `extractColumns` 
  * `update-progress` CSS-only progress bar

### /pages/tableOverview

List of the existing tables with some filtering capabilities, One of the two "main" app pages.

### /pages/template

Define a new template and specify which tables should it use

### /pages/templateDetails

A page with several large sections that defines all properties of  certain template in CS. 

### /pages/templateOverview

One of the two "main" pages, a default page for CS, contains a list of all existing templates + some filtering options

### /pages/templateSelection

Selecting templates to be exported. Under `blocks`, contains:

  * `recentDownloads` A list of recently downloaded export results

### /pages/trackingTemplate

Tracking template settings

### /pages/wizard

Setup Wizard that runs in a modal and guides new users through multiple steps.

### styleguide

Live Styleguide and related files.

### External dependencies

Some explanation for external dependencies.

Automatically managed:

  * `b_` A library to create BEM compliant CSS classes easily without ugly concatenation. Used in React components but can be used anywhere we work with BEM.
  * `backbone.localStorage` Use local storage via Backbone
  * `d3` SVG graphs
  * `dustjs-linkedin` Dust templating engine
  * `dustjs-linkedin-helpers` Extra helpers for Dust
  * `es5-shim` Twitter Flight wants to have it (most likely, not necessary with our browser requirements)
  * `exoskeleton` Backbone on steroids build for modern browsers, with a compatible API
  * `flight` Twitter Flight library
  * `jquery` jQuery. We don't use this one, however, as we load jQuery from Google CDN.
  * `jquery-bemhelpers` jQuery plugin to work with BEM modifiers
  * `jspath` JSON data extraction library inspired by XPath, very powerful and fast. Use it!
  * `react` React.js
  * `reggirt` jQuery plugin to trigger events in reversed direction, capturing-phase-like (`'reggirt' === 'trigger'.split('').reverse().join('')`)
  * `require-css` Use require.js to manage CSS dependencies
  * `requirejs` Require.js
  * `rivets` Rivets.js, a library + templates for two-way binding
  * `rivets-backbone-adapter` Make rivets.js work with backbone models
  * `text` Require.js plugin to build external textual dependencies (templates in our case) into resulting JS files
  * `underscore` Underscore library
  * `validate` Declarative validation for JS data
  * `x-responsable-grid` LESS-based simple semantic grid
   
Manually managed:

  * `6to5` ES6 compiler which is now called Babel, pls consider upgrading
  * `dust-motes` Additional Dust.js helpers
  * `glyphicons-halflings` Icons
  * `jquery-outside-events` jQuery plugin implementing `clickoutside` and similar events
  * `jquery.debounce-1.0.5` Throttle and debounce functions
  * `json-tree` Display a JSON tree 
  * `laces.js` Deprecated two-way binding library
  * `nvd3` Draw graphs in SVG with `d3`
  * `promise-queue` Promise queue implementation
  * `RequireCSS` RequireJS plugin to manage CSS
  * `slick` Slick Grid (with local changes, do not auto-update)
  * `susanin` Routing library inspired by a famous Russian hero Ivan Susanin, who routed a whole army of Frenchmen into deep woods in during the Patriotic War of 1812. The route did not end well for both of them.

## Dependency management

CS uses Bower to manage frontend dependencies whenever possible.

Install new packages and save them locally:

```
bower install --save packagename
```

Currently all Bower dependencies are committed to the repo. This is intentional. We also typically freeze versions.

Build dependencies like `gulp` are managed by `npm`. Those dependencies reside under `node_modules` and are not currently committed to the repo.

To install all existing deps locally, run

```
npm install
```

To install new dependencies, run

```
npm install --save-dev packageName
```

### Require JS

CS uses Require.js (AMD) for managing JS and CSS dependencies as well as templates.

In the development environment, all dependencies are resolved locally and dynamically; for production, a build process
is necessary to compile JS and CSS into standalone files. Templates are compiles into the resulting JS file as resources.

Please note that there are two separate (but very similar) configurations for require.js:

  * `./build.js` for the production build
  * `./common/require-config.js` for dev/runtime dependencies
  
Make sure you update both when dependencies are changed!

Most libraries are compiled into the resulting JS file and not loaded separately. The resulting file is aggressively cached.

## Frontend assets building

We have to build the following assets:

  1. LESS into CSS
  1. JSX (React) and ES6 into JavaScript
  1. JS modules into the resulting single JS file
  1. CSS into one file
  1. Compile templates into JS files so we don't have to load them separately

(1) and (2) are done with Gulp, all the rest are part of the production building process.

Each time a change is made, frontend assets should be recompiled manually or with a watcher (see below).

### Confuguring the environment

  1. Install `node` and `npm` if you don't have it yet.
  1. Run `npm install` in the project repo root to get all dependencies
  1. Run `npm install -g gulp` to install `gulp` globally
  1. Run `npm install -g bower` to install Bower globally

### Compiling locally with Gulp

There are the following commands available:

`gulp less` Recompile all LESS assets
`gulp es6` Recompile .jsx and .es6 files
`gulp watch` Start a local file watcher that detects any change to your `*.jsx|*.es6` or `*.less` files and runs the compilation immediately
 
### Compiling for production
 
Run the `build.source` script in the root project folder.
The minified files (*.min) are what you need for deploy.

### Updating dependencies in npm

NPM uses semantic versioning (semver) to specify dependencies. It means the dependencies are resolved in such a way that 
some minor updates and bugfixes are allowed. That means different people who set their environments at different times
may end up having different package versions on their machines, and those will do the compilation differently.

We don't have this problem with Bower as we keep all the deps under version control, and developers are not supposed to
run `bower update` without reason. It's not the case with `npm` as we don't commit the dependencies.

There's a workaround for this problem called `npm shrinkwrap --dev`. If you run this command, a new file is created alongside
your `package.json` which is called `npm-shrinkwrap.json`. This file contains precise information about exact versions
of each of your packages and their existing dependencies, for the exact moment you run that command.
 
 hen you run `npm install`, this file is taken into account and "overrides" `package.json`, so you get exactly the versions
 you expect. When the project lead decides to get some updates, `npm-shrinkwrap.json` can be deleted. Running `npm update`
 afterwards will bring you all the normal updates available under your specific `semver` configuration. if you like the results,
 you can generate a new shrinkwrap file, commit it, and freeze the new setup till you need to update it the next time.
 
### Updateing dependencies in Bower

Run `bower update` and commit the results if you like them.

## General Frameworks overview

For historical reasons, the CS app started on top of a custom framework, then was mainly evolved as a Twitter Flight based app,
then some MVC was introduced with Rivets.js and Backbone, but that was considered not flexible enough. We ended up deciding
 to go on with React.js, but only a small fraction of components were implemented in React.

So, the state of frameworks used in the codebase is like this:

  * `React.js` The recommended technology for any new module or page
  * `Twitter Flight` Deprecated, but as almost all the modules are implemented with it, a smooth migration path is needed.
  * `laces` Strongly deprecated
  * `Rivets.js` Deprecated. New pages/modules should NOT be created with Rivets, use React instead.

## BEM architecture in CSS and JS

This app uses BEM Methodology to structure CSS and — to a certain extent — components and JS code.

Usign BEM is what keeps CSS in this complicated app maintenable. About 95% or more of the whole CSS codebase is strictly
 compliant with BEM naming and methodology.
 
We use the naming scheme introduced by Yandex, which is `block__elem_mod`. Most of the articles on the Web use a different
variation of the naming: `block__elem--mod` and also don't support key/value modifiers `block__elem_modName_modVal` but
all the principles are the same.
 
In JavaScript, we keep code close to concept of blocks, elements and modifiers, sometimes binding action to certain
modifier updates using `bemhelpers` jQuery plugin, and try to keep the markup interaction BEM-based.

Read more about BEM in our styleguide and on these web sites:

  * http://getbem.com (Community project, recommended)
  * http://bem.info (Official Yandex project with a stronger focus on BEM-related tools, English version is not always perfect)

## Grid system and layout guidelines

We use semantic grid approach. This means that we typically do not use CSS classes from a grid library to create a grid;
instead, we mix LESS-based macros with the styles of blocks or elements that need to receive grid properties. At the price
of slightly increasing the overhead in compiled styles (because of repeating declarations) we end up with a very flexible 
 and maintenable CSS codebase where grid properties are integral parts of the styles.
 
`x-responsable-grid` is the grid based on a set of LESS mixins that is used in most of the app components.
 
 Flexboxes are sometimes an easier and better alternative. Because our browser requirements fully allow for flexboxes
 to be used without any significant hacks, this is an alternative approach which is at least as good as grid mixins.

## Dust.js templates

React.js and Rivets.js both have their templating systems, but Twitter Flight does not.

Dust.js templates are used whenever it's not a React or a Twitter Flight component. Make sure we use a fork of Dust 
made by LinkedIn. This fork is rapidly evolving so keep an eye on applying updates, a lot of templates can be broken
 or require fixes. Unfortunately, according to our experience, Dust.js does not have a perfect record on keeping
  backward compatibility or being completely bug-free or side-effect-free.

## Rivets.js two-way binding

This was an attempt to find a stricter MVC solution that would eliminate the constant problem of keeping UI up-to-date
with the data in the application flow. A Backbone-compatible library called Exoskeleton is used for models.

Although several pages and components were implemented with Rivets and work quite well, we decided that the resulting code
is not easy to maintain and does not provide enough flexibility for complex interactions we need. At the same time,
React.js offers all of the features we wanted from Rivets and does not have its disadvantages.

Using Rivets for new pages/components is not recommended.

## React.js and ES6

Only a handful of components were implemented using React. However, some changes were introduced because of this:

  * JSX format is used to write React modules, so it has to be compiled
  * ES6 (ES2015) is also supported now as we need to compile the code anyway. 

See code for live examples.

No FLUX or similar data flow was introduced yet, but if React codebase will continue to grow, that's what should be done
with a very high priority. Having a well-defined data flow is critical for React-based projects where data plays the central role.

## Future milestones

Given that enough resources are available, the following targets for changes/refactoring should be considered:

  * Getting rid or Rivets code base and switching it to React
  * Introducing a well-defined data flow within the app (consider FLUX https://facebook.github.io/flux/)
  * Switching to server push (Web Sockets or a similar technology) as many interactions will benefit from it
  * Modularize large pages even more
  * Enable regular library updates: React, Dust, Twitter Flight, jQuery and many other tools will benefit from new versions (this may also be necessary to support newer browsers)

## Styleguide
 
Most of the shared app blocks are represented in a "live styleguide" placed under `/styleguide`.
This uses the same codebase as the project itself and should be regularly extended with new examples, new blocks etc.



