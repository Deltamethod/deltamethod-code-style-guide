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

## Modularization

CS uses Require.js (AMD) for managing JS and CSS dependencies as well as templates.
In the development environment, all dependencies are resolved locally and dynamically; for production, a build process
is necessary to compile JS and CSS into standalone files. Templates are compiles into the resulting JS file as resources.

Please note that there are two separate (but very similar) configurations for require.js:

  * `./build.js` for the production build
  * `./common/require-config.js` for dev/runtime dependencies
  
Make sure you update both when dependencies are changed!

Most libraries are compiled into the resulting JS file and not loaded separately. The resulting file is agressively cached.

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
   * `head` The page header + internal blocks under `block`:
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
  * `extractColumns`
  * `feedColumnSelect`
  * `google-account-autofill`
  * `hiddenColumns`
  * `importFeed`
  * `importTable`
  * `restoreTable`
  * `slick-grid`
  * `slick-grid-filter`
  * `slick-grid-multi-filter`
  * `tableTypeSelect`
  * `update-progress`

### /pages/tableOverview
### /pages/template
### /pages/templateDetails
### /pages/templateOverview
### /pages/templateSelection
### /pages/trackingTemplate
### /pages/wizard

styleguide

### Pages

### External dependencies


## Dependency management


### External tools


### Require JS



## Frontend assets building



## General Frameworks overview
## Twitter Flight guide lines
## BEM architecture in CSS and JS
## Grid system and layout guidelines
## Dust.js templates
## Rivets.js two-way binding


## React.js and ES6
## Future milestones
## Styleguide and version control 
## Things to watch for


