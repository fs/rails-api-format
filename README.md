# Rails API Format

## About

`Rails API Format` works together with [ActiveModelSerializers](https://github.com/rails-api/active_model_serializers) and [Responders](https://github.com/plataformatec/responders) gems and makes JSON response compatible with [REST API Design](https://github.com/fs/rails-api-format/wiki/REST-API-Design).

For example, instead of this:

```
{ "error" => "Invalid email or password." }
```

your response will look like this:

```
{
  "error" => {
    "id" => "c6db702b-e6ea-4189-ae3e-e7ddb775cc31",
    "status" => 401,
    "error" => "Invalid email or password."
  }
}
```

## Install

Add this line to your application's Gemfile:

```
gem "rails-api-format", git: "https://github.com/fs/rails-api-format.git"
```

and then execute:

```
$ bundle
```

## Usage

After installing you should not to do anything special, response will be [standardized](https://github.com/fs/rails-api-format/wiki/REST-API-Design) automatically.

Also you can build your own error object to respond with, e.g.:

```
RailsApiFormat::Error.new(status: :not_found, error: I18n.t("activity.errors.user.not_found"))
```

Another handy thing is that how you can check invalid response in your acceptance specs.
Insted of this:

```
expect(respons["error"]).to eq("Invalid email or password.")
```

you can use built in matcher:

```
expect(response).to be_an_error_representation(:unauthorized, "Invalid email or password.")
```

## Getting Help

If you find a bug, please report an [Issue](https://github.com/fs/rails-api-format/issues/new).

## Credits

`Rails API Format` is maintained by [Timur Vafin](http://github.com/timurvafin).
It was written by [Flatstack](http://www.flatstack.com) with the help of our
[contributors](http://github.com/fs/rails-api-format/contributors).

[<img src="http://www.flatstack.com/logo.svg" width="100"/>](http://www.flatstack.com)

