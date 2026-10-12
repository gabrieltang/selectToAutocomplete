# Using the location selector in a Shopify theme

This folder contains a ready-to-paste section for Shopify Online Store 2.0
themes (Dawn and most current themes). It shows a "Where are you located?"
form with the type-ahead country selector, inside a Shopify contact form, so
submissions arrive at the store's contact email with the chosen country.

The repository zip cannot be uploaded as a theme on its own: Shopify expects a
theme folder structure and does not serve standalone HTML pages. Use the steps
below instead.

## Install

1. In Shopify admin go to **Online Store → Themes**, open the **…** menu on
   your theme and choose **Edit code**.
2. Under **Assets**, click **Add a new asset** and upload these four files from
   the root of this repository, one at a time:
   - `jquery-1.11.1.min.js`
   - `jquery-ui.min.js`
   - `jquery.select-to-autocomplete.js`
   - `jquery-ui.css`
3. Under **Sections**, click **Add a new section**, name it
   `location-selector`, and replace the generated contents with
   `shopify/sections/location-selector.liquid` from this folder. Save.
4. Go to **Online Store → Themes → Customize**, open the page you want (for
   example a new page using the default page template), click **Add section**
   and pick **Location selector**.
5. Adjust the heading, labels, placeholder and padding in the section settings
   and save.

Submissions use Shopify's contact form, so they are sent to the sender email
set under **Settings → Notifications**, with the country shown under the
label chosen in the "Field name in the contact email" setting.

## jQuery

The section loads the bundled jQuery 1.11.1 by default and, if the theme
already had a jQuery on the page, hands the global `jQuery` and `$` back to
the theme's copy once the plugin is set up. This is safe for Dawn and other
themes that ship without jQuery, and for older themes that load their own.

Turn off **Load bundled jQuery** only if your theme loads jQuery in the page
`<head>` and you want to avoid the extra download. If the theme loads its
jQuery at the end of the page instead, keep the setting on: the section runs
before the theme's copy exists.

## Limits

- **Checkout cannot be customised this way.** Shopify checkout does not allow
  theme JavaScript, so this selector works on storefront pages, contact forms
  and account pages, but not on the checkout address form.
- **Customer address forms** use Shopify's own `country_option_tags` select,
  which has no alternative-spelling attributes. You can still call
  `selectToAutocomplete()` on it for type-ahead, but synonyms such as "UK" or
  "Deutschland" only work if you add `data-alternative-spellings` to its
  options with a small script first.
- Add the section once per page. Each copy loads the scripts again.

## Using the selector in your own form

Copy the `<select>` block from the section (including every `<option>`) into
any form in your theme, give it an id, load the same four assets, and run:

```liquid
{{ 'jquery-1.11.1.min.js' | asset_url | script_tag }}
{{ 'jquery-ui.min.js' | asset_url | script_tag }}
{{ 'jquery.select-to-autocomplete.js' | asset_url | script_tag }}
{{ 'jquery-ui.css' | asset_url | stylesheet_tag }}
<script>
  jQuery(function ($) {
    $('#your-country-select').selectToAutocomplete();
  });
</script>
```

The select stays in the form (hidden) and is what gets submitted. The plugin
inserts a visible text field after it and copies the select's `class`,
`placeholder` and `autocomplete` attributes onto it, so style the select's
class and the text field follows.
