Laravel Blade Vue Directive Fork (PHP >= 5.6.4)
==============

Laravel Blade Vue Directive provides blade directives for vue.js single file and inline template components.

<!-- MarkdownTOC autolink="true" autoanchor="true" bracket="round" -->

- [Usage](#usage)
    - [Basic Example](#basic-example)
    - [Scalars Example](#scalars-example)
    - [Booleans and Numbers Example](#booleans-and-numbers-example)
    - [Objects and Arrays Example](#objects-and-arrays-example)
    - [camelCase to kebab-case](#camelcase-to-kebab-case)
    - [Using compact to pass variables directly through](#using-compact-to-pass-variables-directly-through)

<!-- /MarkdownTOC -->

<a id="usage"></a>
## Usage

The Laravel Blade Vue Directive was written to be simple and intuitive to use. It's not opinionated about how you use your vue.js components. Simply provide a component name and (optionally) an associative array of properties.

For Laravel 5.5 and later, the package will automatically register. If you're using Laravel before 5.5, then you need to add the provider to the providers array of `config/app.php`.

```php
  'providers' => [
    // ...
    Jhoff\BladeVue\DirectiveServiceProvider::class,
    // ...
  ],
```

<a id="basic-example"></a>
### Basic Example

Using the vue directive with no arguments in your blade file

```html
    @vue('my-component')
        <div>Some optional slot content</div>
    @endvue
```

Renders in html as

```html
    <component v-cloak is="my-component">
        <div>Some optional slot content</div>
    </component>
```

Note that the contents between the start and end tag are optional and will be provided as [slot contents](https://vuejs.org/v2/guide/components-slots.html). To use an inline-template, use the `@inlinevue` directive instead:

```html
    @inlinevue('my-component')
        <div>Some inline template content</div>
    @endinlinevue
```

Renders in html as

```html
    <component inline-template v-cloak is="my-component">
        <div>Some inline template content</div>
    </component>
```

<a id="scalars-example"></a>
### Scalars Example

Using the vue directive with an associative array as the second argument will automatically translate into native properties that you can use within your vue.js components.

```html
    @vue('page-title', ['title' => 'Welcome to my page'])
        <h1>@{{ title }}</h1>
    @endvue
```

Renders in html as

```html
    <component v-cloak is="page-title" title="Welcome to my page">
        <h1>{{ title }}</h1>
    </component>
```

Then, to use the properties in your vue.js component, add them to `props` in your component definition. See [Component::props](https://vuejs.org/v2/guide/components.html#Props) for more information.

```javascript
    Vue.component('page-title', {
        props: ['title']
    });
```

<a id="booleans-and-numbers-example"></a>
### Booleans and Numbers Example

Properties that are booleans or numeric will be bound automatically as well

```html
    @vue('report-viewer', ['show' => true, 'report' => 8675309])
        <h1 v-if="show">Report #@{{ report }}</h1>
    @endvue
```

Renders in html as

```html
    <component v-cloak is="report-viewer" :show="true" :report="8675309">
        <h1 v-if="show">Report #{{ report }}</h1>
    </component>
```

<a id="objects-and-arrays-example"></a>
### Objects and Arrays Example

The vue directive will automatically handle any objects or arrays to make sure that vue.js can interact with them without any additional effort.

```html
    @vue('welcome-header', ['user' => (object)['name' => 'Jordan Hoff']])
        <h2>Welcome @{{ user.name }}!</h2>
    @endvue
```

Renders in html as

```html
    <component v-cloak is="welcome-header" :user="{&quot;name&quot;:&quot;Jordan Hoff&quot;}">
        <h2>Welcome {{ user.name }}!</h2>
    </component>
```

Notice that the object is json encoded, html escaped and the property is prepended with `:` to ensure that vue will bind the value as data.

To use an object property in your component, make sure to make it an `Object` type:

```javascript
    Vue.component('welcome-header', {
        props: {
            user: {
                type: Object
            }
        }
    });
```

<a id="camelcase-to-kebab-case"></a>
### camelCase to kebab-case

If you provide camel cased property names, they will automatically be converted to kebab case for you. This is especially useful since vue.js will [still work](https://vuejs.org/v2/guide/components.html#camelCase-vs-kebab-case) with camelCase variable names.

```html
    @vue('camel-to-kebab', ['camelCasedVariable' => 'value']])
        <div>You can still use it in camelCase see :) @{{ camelCasedVariable }}!</div>
    @endvue
```

Renders in html as

```html
    <component inline-template v-cloak is="camel-to-kebab" camel-cased-variable="value">
        <div>You can still use it in camelCase see :) {{ camelCasedVariable }}!</div>
    </component>
```

Just make sure that it's still camelCased in the component props definition:

```javascript
    Vue.component('camel-to-kebab', {
        props: ['camelCasedVariable']
    });
```

<a id="using-compact-to-pass-variables-directly-through"></a>
### Using compact to pass variables directly through

Just like when you render a view from a controller, you can use compact to pass a complex set of variables directly through to vue:

```html
    <?php list($one, $two, $three) = ['one', 'two', 'three']; ?>
    @vue('compact-variables', compact('one', 'two', 'three'))
        <div>Variables are passed through by name: @{{ one }}, @{{ two }}, @{{ three }}.</div>
    @endvue
```

Renders in html as

```html
    <component inline-template v-cloak is="compact-variables" one="one" two="two" three="three">
        <div>Variables are passed through by name: {{ one }}, {{ two }}, {{ three }}.</div>
    </component>
```

Then in vue, make sure to define all of your properties:

```javascript
    Vue.component('compact-variables', {
        props: ['one', 'two', 'three']
    });
```
