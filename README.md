# Sage ACF Gutenberg Blocks
Generate ACF Gutenberg blocks just by adding templates to your Sage theme.

This package is based heavily on [this article](https://medium.com/nicooprat/acf-blocks-avec-gutenberg-et-sage-d8c20dab6270) by [nicoprat](https://github.com/nicooprat).

## Installation
Run the following in your Sage (v9 or 10) based theme directory:
```sh
composer require "larodiel/sage-acf-gutenberg-blocks"
```

## Creating blocks
Add blade templates to `views/blocks` which get and use ACF data. Each template requires a comment block with some data in it:
```blade
{{--
  Title:
  Description:
  Category:
  Icon:
  Keywords:
  Mode:
  Align:
  PostTypes:
  SupportsAlign:
  SupportsMode:
  SupportsMultiple:
  SupportsFullHeight:
  SupportsInnerBlocks:
  EnqueueStyle:
  EnqueueScript:
  EnqueueAssets:
  Parent:
--}}
```

### Example block template

```blade
{{--
  Title: Testimonial
  Description: Customer testimonial
  Category: formatting
  Icon: admin-comments
  Keywords: testimonial quote
  Mode: edit
  Align: left
  PostTypes: page post
  SupportsAlign: left right
  SupportsMode: false
  SupportsMultiple: false
  SupportsFullHeight: false
  SupportsInnerBlocks: false
  EnqueueStyle: styles/style.css
  EnqueueScript: scripts/script.js
  EnqueueAssets: path/to/asset
  Parent: core/column
--}}

<blockquote data-{{ $block['id'] }} class="{{ $block['classes'] }}">
    <p>{{ get_field('testimonial') }}</p>
    <cite>
      <span>{{ get_field('author') }}</span>
    </cite>
</blockquote>

<style type="text/css">
  [data-{{$block['id']}}] {
    background: {{ get_field('background_color') }};
    color: {{ get_field('text_color') }};
  }
</style>
```

## Data Options

The options in the file header map to options in the [`acf_register_block_type`  function](https://www.advancedcustomfields.com/resources/acf_register_block_type/).

| Field              | Description                                      | Values     | Notes |
| ------------------ | ------------------------------------------------ | ---------- | ----  |
| `Title`            | Title of the block in the gutenberg editor | i.e. `Testimonial` | _required_ |
| `Description`      | Description of the block in the gutenberg editor | i.e. `My testimonial block` | _optional_ |
| `Category`         | Category to store the block in. Use these values or [register your own custom block categories](#creating-your-custom-blocks-categories) | `common`, `formatting`, `layout`, `widgets`, `embed` | _required_ |
| `Icon`             | An icon property can be specified to make it easier to identify a block. Uses [dashicons](https://developer.wordpress.org/resource/dashicons/) | i.e. `book-alt` | _optional_ |
| `Keywords`         | An array of search terms to help user discover the block while searching. Separate values with a space. | i.e. `quote mention cite` | _optional_ |
| `Mode`             | The display mode for your block. auto: Preview is shown by default but changes to edit form when block is selected. preview: Preview is always shown. Edit form appears in sidebar when block is selected. edit: Edit form is always shown. | `auto`, `preview` or `edit` |_optional_ (defaults to `preview`) |
| `Align`            | The default block alignment. | `left center right wide full` |_optional_ (defaults to empty string)
| `PostTypes`        | An array of post types to restrict this block type to. Separate values with a space. | i.e. `post page` |
| `SupportsAlign`    | This property adds block controls which allow the user to change the block’s alignment. Set to true to show  all alignments, false to hide the alignment toolbar. Set to an array (strings separated by spaces) of specific alignment names to customize the toolbar. | (boolean) `true`, `false`<br> or (array) `left center right wide full` | _optional_ (defaults to true) |
| `SupportsMode`     | This property allows the user to toggle between edit and preview modes via a button. | `true`  or `false` |_optional_ (defaults to `true`) |
| `SupportsMultiple` | This property allows the block to be added multiple times. | `true`  or `false` |_optional_ (defaults to `true`) |
| `SupportsFullHeight` | This property allows enables the full height button on the toolbar of a block  | `true`  or `false` |_optional_ (defaults to `false`) |
| `SupportsInnerBlocks` | This property allows the block to support the nesting of other blocks within it. | `true`  or `false` |_optional_ (defaults to `false`) ___This works just on preview mode___ |
| `SupportsAlignText` | This property adds an alignment toolbar button similar to that seen when editing a paragraph of text. | `true`  or `false` |_optional_ (defaults to `false`) |
| `SupportsAlignContent` | This property adds an alignment toolbar button similar to that seen when editing a core "Cover block" | `true`, `false` or matrix (Show full alignment matrix in toolbar) |_optional_ (defaults to `false`) |
| `EnqueueStyle` | A CSS file to load when the block is used. | e.g. `styles/my-block.css` |_optional_ (defaults to empty string) |
| `EnqueueScript` | A JS file to load when the block is used. | e.g. `scripts/my-block.js` |_optional_ (defaults to empty string) |
| `Parent` | An array of block types to restrict where this block can be used. Separate values with a space. | e.g. `core/column acf/parent-block` |_optional_ (defaults to usable anywhere) |

## Creating ACF fields
Once a block is created you'll be able to assign ACF fields to it using the standard Custom Fields interface in WordPress. We recommend using [sage-advanced-custom-fields](https://github.com/MWDelaney/sage-advanced-custom-fields) to keep your ACF fields in version control with Sage.

## Filter block data
Block data can be altered via the 'sage/blocks/[block-name]/data' filter. For example, if your block template is called `my-block.blade.php`, you can alter the data this way:

```php
add_filter('sage/blocks/my-block/data', function ($block) { /* Do your thing here. */ });
```

## Filter template folders
By default all your template files in `views/blocks` will be loaded. You can use the templates filter to add more folders if you wish. See an example below of how to add your own folders.

```php
add_filter('sage-acf-gutenberg-blocks-templates', function ($folders) {
    $folders[] = 'views/your-folder'; // Adds your folder
    return $folders;
});
```

## Creating your custom blocks categories

```php
add_filter('block_categories_all', function ($categories) {
    // Define the new categories
    $newCategories = [
        ['slug' => 'my-custom-category', 'title' => __('Custom Category', 'sage')],
        ['slug' => 'my-custom-category2', 'title' => __('Custom Category 2', 'sage')],
    ];

    // Merge the new categories with the existing ones
    return array_merge($categories, $newCategories);
});
```
More details about the hook `block_categories_all` on [registering your own custom block categories](https://developer.wordpress.org/reference/hooks/block_categories_all/)
