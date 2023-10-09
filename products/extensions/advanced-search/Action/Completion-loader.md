---
nav:
  title: Define a completion loader
  position: 130

---

# Action completion loader
Advanced Search does not use default Elasticsearch completion, we implement the [completion](../How-to-modify-completion.md) ourselves.

We add action search terms as completion using `ActionCompletionLoader`.

@refer: `\Shopware\Commercial\AdvancedSearch\Domain\Action\ActionCompletionLoader`

# Define a completion loader

You can define a custom completion loader to extend the capabilities of the search suggestions and add your own search terms.
To do this, create a class that extends the `AbstractCompletionLoader` class.

Here's an example:

```php
<?php declare(strict_types=1);

namespace YourPluginNameSpace;

use Shopware\Commercial\AdvancedSearch\Domain\Completion\AbstractCompletionLoader;
use Shopware\Core\Framework\Plugin\Exception\DecorationPatternException;
use Shopware\Core\System\SalesChannel\SalesChannelContext;
use Symfony\Component\HttpFoundation\Request;

class YourCustomCompletionLoader extends AbstractCompletionLoader
{
    public function getDecorated(): AbstractCompletionLoader
    {
        throw new DecorationPatternException(self::class);
    }

    public function load(string $term, SalesChannelContext $context): array
    {
        if (trim($term) === '') {
            return [];
        }

        $request = new Request();
        $request->query->set('search', $term);

        $searchTerms = [];
        
        // Add your own logic to load the search terms
        return $searchTerms;
    }
}
```

Replace **YourPluginNamespace** with the actual namespace of your plugin.

In your custom completion loader class, you need to implement the `load` method. This method is responsible for loading the search terms based on the provided term and sales channel context.

And register it in the container with tag `advanced_search.completion_loader`.

```xml
# YourPluginNameSpace should be changed to your respectively completion loaders
<service id="YourPluginNameSpace\YourCustomCompletionLoader">
<tag name="advanced_search.completion_loader" priority="1000"/>
</service>
```

Be aware that the **priority** attribute is used to determine the order of the completion loaders.
The higher the priority, the earlier the completion loader is executed, and **the higher the position** the search terms will have in the search suggestions.
