---
id: 68
title: "New in Symplify 3: DocBlock Cleaner Fixer"
perex: |
    Focus on docblock has increased thanks to [PHP 7 scalar types](https://php.net/manual/en/migration70.new-features.php#migration70.new-features.scalar-type-declarations) and PHPStan with [intersection and union types](https://medium.com/@ondrejmirtes/phpstan-0-9-a-huge-leap-forward-1e9b0872d1cc). Thanks to that, more and more **docblocks become just visual noise** causing [cognitive overload](https://en.wikipedia.org/wiki/Cognitive_load).
    <br><br>
    Symplify 3 introduces a new help hand - **fixer that cleans doc block noise for you and makes your code more valuable to the reader**.
tweet: "Do you use PHP 7 and scalar types? Do you still see value in your docblocks? Which is useful and which is legacy? Symplify 3 introduces a new fixer, that helps you to clean the later! #codingstandard #phpcsfixer"
tweet_image: "/assets/images/posts/2017/doc-block-cleaner/diff.png"

updated: true
updated_since: "April 2018"
updated_message: |
    Updated with <a href="https://github.com/Symplify/Symplify/blob/master/CHANGELOG.md#v400---2018-04-02">ECS 4.0</a>, Neon to YAML migration and <code>checkers</code> to <code>services</code> migration.
---

<blockquote class="blockquote text-center">
    ...when you program, you have to <strong>think about how someone will read your code</strong>, not just how a computer will interpret it.
    <footer class="blockquote-footer">
        <a href="https://en.wikiquote.org/wiki/Kent_Beck">Kent Beck</a>, creator of the Extreme Programming and Test Driven Development
    </footer>
</blockquote>

<br>

Do you find similar patterns in your code?

<br>


```php
/**
 * @param string $name
 */
public function addVisitor(string $name)
{
    // ...
}
```

<br>

```php
/**
 * @param InputInterface $input An Input instance
 */
public function getArguments(InputInterface $input)
{
    // ...
}
```

<br>


```php
/**
 * @param array $items
 */
public function prependItems(array $items)
{
    // ...
}
```

<br>


```php
/**
 * @return boolean
 */
public function getStorage(): bool
{
    // ...
}
```

<br>


Do you know what do they have in common?
**They only duplicate the typehint information and bring no extra value to the reader**.


<div class="text-center">
    <img src="/assets/images/posts/2017/doc-block-cleaner/use-only-what-you-need.jpg" class="img-thumbnail">
</div>

*No big deal* you might say as code author. But your code is much more read that written, so making it as readable as possible should be your priority.

<br>

## How to Remove <strike>Manually</strike> Automatically?

Cleaning every single case would be crazy. Luckily, we **live in CLI-refactoring generation**,
so all we need is new Fixer from `Symplify\CodingStandard` 3.0 - `Symplify\CodingStandard\Fixer\Commenting\RemoveUselessDocBlockFixer`.

<a href="https://github.com/Symplify/Symplify/pull/427" class="btn btn-dark btn-sm">
    <em class="fab fa-github fa-fw"></em>
    See pull-request #427
</a>

This fixer scans docs blocks, compares it with code types, evaluates value of each one (like you would do) and **drops** those, which do not add any extra value and only slow down the code reading time.


### Tested on many Open-Source Projects

Docblocks don't have any standard format, so I **first tested this Fixer** on handful of PHP open-source projects. **Open them** to see, what this fixer can do in real code:

- [php-ml](https://github.com/php-ai/php-ml/pull/145) and [other PR](https://github.com/php-ai/php-ml/pull/146)
- [ReflectionDocBlock](https://github.com/phpDocumentor/ReflectionDocBlock/pull/137)
- and [Symfony](https://github.com/symfony/symfony/pull/24931)


Thanks to that Fixer now **covers dozens of edge cases**.


## Challenge Your Code

**1. Install**

```bash
composer require symplify/easy-coding-standard --dev
```

**2. Create `easy-coding-standard.yml`**

```yaml
# easy-coding-standard.yml
services:
    Symplify\CodingStandard\Fixer\Commenting\RemoveUselessDocBlockFixer: ~

    # works best with these checkers, to remove empty docblock
    Symplify\CodingStandard\Fixer\Commenting\RemoveSuperfluousDocBlockWhitespaceFixer: ~
    Symplify\CodingStandard\Fixer\Commenting\RemoveEmptyDocBlockFixer: ~
```

**3. Run it**

```bash
vendor/bin/ecs check src
```

**4. See the diff**

<div class="text-center">
    <img src="/assets/images/posts/2017/doc-block-cleaner/diff.png" class="img-thumbnail">
</div>

**5. And fix it**

```bash
vendor/bin/ecs check src --fix
```

<br>

Don't you like `mixed` or `object`? The fixer is [configurable](https://github.com/Symplify/CodingStandard#block-comment-should-only-contain-useful-information-about-types-wrench), so you can set types that you'd like to remove.

```yaml
# easy-coding-standard.yml
services:
    Symplify\CodingStandard\Fixer\Commenting\RemoveUselessDocBlockFixer:
        useless_types: ['mixed', 'object'] # [] by default
```

<br>

Happy eye resting!
