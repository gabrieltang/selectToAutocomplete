# Using the location selector in a Shopify theme

This folder contains two ready-to-paste sections for Shopify Online Store 2.0
themes (Dawn and most current themes):

- `sections/location-selector.liquid`: a "Where are you located?" form with
  the type-ahead country selector inside a Shopify contact form, so
  submissions arrive at the store's contact email with the chosen country.
- `sections/shipto-redirect.liquid`: a "Where should we ship to?" prompt that
  resolves the chosen country to one of your webstores and sends the visitor
  there. Stores are blocks in the theme editor, so nobody edits JavaScript to
  change them. See [Ship-to redirect](#ship-to-redirect) below.

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
   `location-selector` (or `shipto-redirect`), and replace the generated
   contents with the matching file from `shopify/sections/`. Save.
4. Go to **Online Store → Themes → Customize**, open the page you want (for
   example a new page using the default page template), click **Add section**
   and pick **Location selector** or **Ship-to redirect**.
5. Adjust the heading, labels, placeholder and padding in the section settings
   and save.

Submissions use Shopify's contact form, so they are sent to the sender email
set under **Settings → Notifications**, with the country shown under the
label chosen in the "Field name in the contact email" setting.

## Ship-to redirect

The **Ship-to redirect** section sends visitors to the store that serves their
country. After adding it:

1. Add one **Store** block per webstore. Give it a name, its URL (for example
   `https://eu.example.com/`) and the countries it serves as ISO 3166-1
   two-letter codes separated by spaces or commas (`DE, FR, NL`). The preset
   comes with US, Canada, UK, EU and Australia/NZ blocks whose URLs you fill
   in.
2. Set the **Fallback store URL** for every country not listed on a block, or
   leave it empty to show the "we do not ship there yet" message instead.
3. Keep the **Country query parameter** as `country` so the destination store
   receives `?country=DE` and can preselect the shipping country or market.
   Set it to empty to send no parameter.

Behaviour settings:

- **Suggest a country from the browser language** preselects a likely country
  (the `GB` in `en-GB`). The visitor can change it before going anywhere.
- **Offer to remember the choice on this device** shows a checkbox and stores
  the chosen country in `localStorage` under the **Storage key**.
- **Redirect returning visitors automatically** sends a visitor with a
  remembered choice straight on when the section loads. It never fires on
  the store it would redirect to, nor inside the theme editor.
- All visible messages are settings and accept `{country}`, `{code}` and
  `{store}` placeholders.

Install the section on every store, with the same store blocks and storage
key, so a visitor who lands on the wrong store can switch and their new
choice is remembered. When the chosen country maps to the store the visitor
is already on, the section says so and does not redirect, which also prevents
redirect loops. In the theme editor, submitting shows the destination URL
instead of navigating.

The query parameter is only a hint: the destination store needs its own code
to read `country` and preselect the shipping country, currency or market. If
all your stores are Shopify Markets inside one shop, use Shopify's
localization form instead of redirecting across domains.

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
- Add one of these sections once per page. Each copy loads the scripts again.

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
