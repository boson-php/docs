# Twig Components

You can use Twig in [your Web Components](web-components-api.md). To do this, 
you need to follow a few simple steps.

### 1. Install Twig

First, you need to install [the Twig](https://twig.symfony.com/) 
itself [using Composer](https://getcomposer.org/).
```bash
    composer require twig/twig
```

### 2. Create Twig Component

After that, you should create a component that supports twig rendering.

```php
use Boson\WebView\Api\WebComponents\ReactiveContext;
use Boson\WebView\Api\WebComponents\WebComponent;
use Boson\WebView\WebView;
use Twig\Environment;
use Twig\TemplateWrapper;

abstract class TwigComponent extends WebComponent
{
    /**
     * In this case, the template will be initialized 
     * once during the first render.
     */
    private TemplateWrapper $template {
        get => $this->template ??= $this->twig->createTemplate(
            template: $this->renderTwig(),
        );
    }

    public function __construct(
        protected readonly Environment $twig,
        ReactiveContext $ctx,
        WebView $webview,
    ) {
        parent::__construct($ctx, $webview);
    }

    abstract protected function renderTwig(): string;

    /**
     * Override the default render behavior by 
     * redirecting it to a Twig template
     */
    #[\Override]
    final public function render(): string
    {
        return $this->template->render(\get_object_vars($this));
    }
}
```


### 3. Create Instantiator

Now we need to define how exactly these components will be created, for 
this we should create our own instantiator, which will return new 
components on demand.

```php
use Boson\WebView\Api\WebComponents\Instantiator\WebComponentInstantiatorInterface;
use Boson\WebView\Api\WebComponents\ReactiveContext;
use Boson\WebView\WebView;
use Twig\Environment;
use Twig\Loader\ArrayLoader;

final readonly class TwigComponentInstantiator implements
    WebComponentInstantiatorInterface
{
    private Environment $twig;

    public function __construct()
    {
        $this->twig = new Environment(new ArrayLoader());
    }

    private function isTwigComponent(string $component): bool
    {
        return \is_subclass_of($component, TwigComponent::class);
    }

    public function create(WebView $webview, ReactiveContext $context): object
    {
        $component = $context->component;

        // Pass twig as a first argument in case of passed 
        // component extends from TwigComponent class 
        if ($this->isTwigComponent($component)) {
            return new $component($this->twig, $context, $webview);
        }

        return new $component($context, $webview);
    }
}
```

### 4. Register Instantiator

To determine that a different instantiator should be used, it can 
be specified in the webview configs.

```php
$webComponentsConfig = new WebComponentsCreateInfo(
    instantiator: new TwigComponentInstantiator(),
);

$applicationConfig = new ApplicationCreateInfo(
    window: new WindowCreateInfo(
        webview: new WebViewCreateInfo(
            webComponents: $webComponentsConfig,
        ),
    ),
);

$app = new Boson\Application($applicationConfig);
```

### 5. Twig Components

And now we can create custom twig components!

```php
class MyTwigComponent extends TwigComponent
{
    protected array $items = [1, 2, 3];

    protected function renderTwig(): string
    {
        return <<<'twig'
            <ul>
                {% for item in items %}
                <li>{{ item }}</li>
                {% endfor %}
            </ul>
        twig;
    }
}
```

To register and check, just write a couple of lines

```php
$app->webview->defineComponent('my-list', MyTwigComponent::class);

$app->webview->html = '<my-list />';
```

![Example Result](/assets/example-twig-components-result.png)