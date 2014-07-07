Json Spec
===================

If you working with JSON-based REST APIs there are several issues:

- You can't simple check is a response is equal to given string as there is things like server-generated IDs
- Matching the whole responses breaks DRY for the spec

This library provides an extensions for PhpSpec and Behat, which aims to solve this issues.

## PhpSpec

JsonSpec extension provides five new PhpSpec matchers:

- `beJsonEqual`
- `includeJson`
- `haveJsonPath`
- `haveJsonType`
- `haveJsonSize`

You may use it in your specs as follow:
```php
class UserSpec extends ObjectBehavior
{

    function let()
    {
        $this->beConstructedWith('Steve', 'Richert');
    }

    function it_includes_names()
    {
        $names = '{"first_name":"Steve","last_name":"Richert"}';
        $this->toJson()->shouldBeJsonEqual($names)->excluding('friends');
    }

    function it_includes_the_ID()
    {
        $this->toJson()->shouldHaveJsonPath('id');
        $this->toJson()->shouldHaveJsonType('integer')->atPath('id');
    }

    function it_includes_friends()
    {
        $this->toJson()->shouldHaveJsonSize(0)->atPath('friends');

        $friend = new User("Catie" , "Richert");
        $this->addFriend($friend);

        $this->toJson()->shouldHaveJsonSize(1)->atPath('friends');
        $this->toJson()->shouldIncludeJson($friend->toJson());
    }

}

```

To install this extension, simply add following into your `phpspec.yml`:
```
extensions:
   - JsonSpec\PhpSpec\Extension
```

That's it.

## Exclusions
Json Spec ingores `id` hash key by default when comparing JSON. It's oftentimes helpful when evaluating JSON representations of newly-created records so that certain values don't have to be known. For example, if you store data in MongoDB, then ID will be always uniqe string. If you want to chane set of excluded keys, you may do this in `phpspec.yml`:
```
json_spec:
    excluded_keys: ['created_at', 'updated_at']
```
With this configuration, the `id` key will be included in JSON comparison, while `created_at` and `updated_at` won't. Keys can also be excluded/included per matcher by chaining the `excluding` or `including` methods (as shown above) which will add or subtract from the globally excluded keys, respectively.

## Paths
Each of JsonSpec matchers deal with JSON "paths." These are simple strings of "/" separated hash keys and array indexes. For instance, with the following JSON:
```javascript
{
  "first_name": "Steve",
  "last_name": "Richert",
  "friends": [
    {
      "first_name": "Catie",
      "last_name": "Richert"
    }
  ]
}

```
We could access the first friend's first name with the path `"friends/0/first_name"`.

## Behat
Behat support coming soon.

## Contributing
If you come across any issues, please [tell me](https://github.com/fesor/json_spec/issues) . Pull requests (with tests) are appreciated. No pull request is too small. Please help with:

- Reporting bugs
- Suggesting features
- Writing or improving documentation
- Fixing typos
- Cleaning whitespace
- Refactoring code
- Adding tests
- Closing [issues](https://github.com/fesor/json_spec/issues)

If you report a bug and don't include a fix, please include a failing test.

## Inspired By
- [json_spec](https://github.com/collectiveidea/json_spec) - Ruby's gem for handling JSON in RSpec and Cucumber

