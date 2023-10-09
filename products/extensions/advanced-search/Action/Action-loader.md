---
nav:
  title: Action loader
  position: 110

---

# Action loader

The `ActionLoader` class is responsible for loading the matching action or search terms for a given search term and sales channel context.

@Refer:

`\Shopware\Commercial\AdvancedSearch\Domain\Action\ActionLoader`

# Define an action loader

To modify the action loader, you can decorate the `ActionLoader` class and add your own logic into it:

```xml
<service id="YourPluginNameSpace\ActionLoaderDecorator" decorates="Shopware\Commercial\AdvancedSearch\Domain\Action\ActionLoader">
    <argument type="service" id=".inner"/>
</service>
```

```php
<?php declare(strict_types=1);

namespace YourPluginNameSpace;

use Shopware\Commercial\AdvancedSearch\Domain\Action\AbstractActionLoader;
use Shopware\Commercial\AdvancedSearch\Entity\SearchAction\SearchActionEntity;
use Shopware\Core\System\SalesChannel\SalesChannelContext;

class ActionLoaderDecorator extends AbstractActionLoader
{
    public function __construct(
        private readonly AbstractActionLoader $decorated
    ) {
    }

    public function getMatchingAction(string $searchTerm, SalesChannelContext $context): ?SearchActionEntity
    {
         // you probably want to tokenize the search term, but it's optional
         $action = $this->decorated->getMatchingAction($searchTerm, $context);
         
         // Add your own logic
        return $action;
    }

    public function getActionSearchTerms(string $searchTerm, SalesChannelContext $context): array
    {
        // you probably want to tokenize the search term, but it's optional
         $searchTerms = $this->decorated->getActionSearchTerms($searchTerm, $context);
         
         // Add your own logic
        return $searchTerms;
    }
}
```

Replace **YourPluginNamespace** with the actual namespace of your plugin.
