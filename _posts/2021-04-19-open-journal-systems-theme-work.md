---
layout: post
permalink: /posts/:year-:month-:day-:title:output_ext
---

Over the past few months I have studied customizing themes for our instance of PKP Open Journal Systems, supporting open access journals hosted by the University of Cincinnati Press and Library Publishing Services. 

Working with a freelance designer, we prototyped a new landing page that included a two-column layout, one column for Journals and one for Proceedings. Digging into the discourse on the support forums for PKP, I found discussions about theme-plugins and journal category functionality, with a good [comment](https://forum.pkp.sfu.ca/t/grouping-journals-by-category-planned-feature-or-tips-on-template-customisation/34629/5) about a quick and dirty solution for achieving a two-column layout. 

Earlier versions of OJS included a journal category function which has since been deprecated. To achieve the split layout, I followed the documentation on [Adding a Theme Option](https://docs.pkp.sfu.ca/pkp-theming-guide/en/theme-options-api#add-a-theme-option) with a text field in which users can input and save journal ids for journals that should be included in the second column.

## Example

Create an array assigned to `$proceedings` variable which will contain integer IDs for titles that should show in the second *proceedings* column.

`/DefaultJournalsUcThemePlugin.inc.php`
```php
$this->addOption('proceedingsArray', 'FieldText', [
  'label' => __('plugins.themes.defaultJournalsUc.option.proceedingsArray.label'),
	'description' => __('plugins.themes.defaultJournalsUc.option.proceedingsArray.description'),
	'default' => ''
]);
```

We create two loops to populate the columns.

`/templates/frontend/pages/indexSite.tpl`
```php
// get $journals object and assign to array for repeated iteration
{assign var=journalsArray value=$journals->toArray()}

...

// loop through system generated `$journals` array and compare with $proceedings array from input field

//column 1
{foreach from=$journalsArray item=journal}
	{assign var="id" value=$journal->getId()}
  {if ! $id|in_array:$proceedings}
    // ... show journal entry
{end}

//column 2
{foreach from=$journalsArray item=journal}
	{assign var="id" value=$journal->getId()}
  {if $id|in_array:$proceedings}
    // ... show proceedings entry
{end}
```

I had some difficulty finding easy access to IDs for journals. Each journal in our instance has a URL that corresponds to the journal initials that are set in configuration. These initials route to a numeric ID for use in the system, e.g. 1,2.3. I was able to expose the journal ID by accessing the setup wizard for each journal, the ID will be exposed in the URL: `localhost:3000/index.php/index/admin/wizard/12`.  

You can see the new theme [https://journals.uc.edu](https://journals.uc.edu/).  
Source code for the theme is on [Github](https://github.com/uclibs/defaultJournalsUc).  

-Sean
