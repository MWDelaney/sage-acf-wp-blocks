# Sage ACF Gutenberg Blocks
Generate ACF Gutenberg blocks just by adding templates to your Sage theme.

## Installation
Run the following in your Sage 9-based theme directory:
```sh
composer require "mwdelaney/sage-acf-gutenberg-blocks"
```

## Creating blocks
Add blade templates to `views/blocks` which get and use ACF data. Each block requires a a comment block with some data in it:
```blade
{{--
  Title: 
  Description: 
  Category: 
  Icon: 
  Keywords: 
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
