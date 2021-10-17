# Add custom card

Starting from Shopware version 6.4.6.0, you are able to add your own card to the administration, that can be displayed in entity detail pages. Your custom card consists of an iframe and within this iframe, your website will be loaded and shown.

In order to create a custom card, you need to define an admin element to define `<admin>` extensions. In there, please add your card by defining a `<card>` element.

* Here you're able to define the source: Please insert the link to your website there.
* To define the card's title and its translation, you can add a label as child element.
* To define the routes of pages where this card should be displayed on, you can add a page as child element.

{% code title="manifest.xml" %}
```markup
<?xml version="1.0" encoding="UTF-8"?>
<manifest xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="https://raw.githubusercontent.com/shopware/platform/master/src/Core/Framework/App/Manifest/Schema/manifest-1.0.xsd">
    <meta>
        ...
    </meta>
    <admin>
        ...
        <card source="https://example.com/iframe/product-card">
            <label>Product card</label>
            <!-- display on product detail page -->
            <page>sw.product.detail.base</page>
            <page>sw.product.detail.specifications</page>
            <!-- additionally show our card on our new tab -->
            <page>sw.product.detail.newTab</page>
        </card>
    </admin>
    ...
</manifest>
```
{% endcode %}

For a complete reference of the structure of the manifest file take a look at the [Manifest reference](../../../../resources/references/app-reference/manifest-reference.md).

If the user opens the card in the administration your app will receive a request to the given source url. Your app can determine the shop that has opened the card through query parameters added to the url: 

* shop-id: The unique identifier of the shop, where the app installed
* shop-url: The URL of the shop, this can later be used to access the Shopware API
* timestamp: The Unix timestamp when the request created
* shopware-shop-signature: sha256 hmac of the rest of the query string, signed with the `shop-secret`

A sample request may look like this:

```text
https://example.com/iframe/product-card?shop-id=HKTOOpH9nUQ2&shop-url=http%3A%2F%2Fmy.shop.com&timestamp=1592406102&shopware-shop-signature=3621fffa80187f6d43ce6cb25760340ab9ba2ea2f601e6a78a002e601579f415
```

In this case the `shopware-shop-signature` parameter contains a sha256 hmac of the rest of the query string, signed again with the secret your app assigned the shop during the [registration](../app-base-guide.md#setup). The signature can be used to verify the authenticity of the request.

## Leave loading state

One last thing in here - the most important of the Javascript part of the view: At first, your new page will display a loading spinner to signalize your iframe is loading. To leave the loading state, your iframe needs to give a notification when the loading process done.

```javascript
function sendReadyState() {
    window.parent.postMessage('sw-app-loaded', '*');
}
```

This has to be done as soon as everything loaded so that the loading spinner disappears. If your view is not fully loaded after 5 seconds, it will be aborted.
