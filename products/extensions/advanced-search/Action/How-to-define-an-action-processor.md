---
nav:
  title: Define an action processor
  position: 120

---

# Define an action processor

You have the flexibility to add custom action processors to handle your own actions in the advanced search functionality.
To do this, you need to create a class that extends the `AbstractActionProcessor` class.

For example:

```php
<?php declare(strict_types=1);

namespace YourPluginNameSpace;

use Shopware\Commercial\AdvancedSearch\Domain\Action\Response\AbstractActionResponse;
use YourPluginNameSpace\YourCustomResponse;
use Shopware\Commercial\AdvancedSearch\Entity\SearchAction\SearchActionEntity;
use Shopware\Core\Framework\Plugin\Exception\DecorationPatternException;
use Shopware\Core\System\SalesChannel\SalesChannelContext;

class YourCustomProcessor extends AbstractActionProcessor
{
    public function getDecorated(): AbstractActionProcessor
    {
        throw new DecorationPatternException(self::class);
    }

    public function process(string $searchTerm, SearchActionEntity $action, SalesChannelContext $context): AbstractActionResponse
    {
        // Implement your own logic here
        
        return new YourCustomResponse(/*your response properties*/, $action->getValidFrom(), $action->getValidTo());
    }
}
```
Replace **YourPluginNamespace** with the actual namespace of your plugin.

YourCustomResponse must extend from one of these two response type:
- ForwardRouteResponse: forward to a route
- RedirectResponse: redirect to a url

And register it in the container with tag `advanced_search.action_type_processor`.

```xml
# YourPluginNameSpace should be changed to your respectively processors
<service id="YourPluginNameSpace\YourCustomProcessor">
<tag name="advanced_search.action_type_processor" key="yourActionType"/>
</service>
```

Replace **yourActionType** with the key representing **your custom action type**.
