# SABE Extend API

The SABE Extend API is based on a [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) architecture which makes SABE Extend API predictable and resource oriented. It uses HTTP built-in features, like HTTP authentication, HTTP verbs (GET, POST, PUT, PATCH, DELETE) and HTTP response codes to allow easy access from any programming language via off-the-shelf libraries and tools.

Last updated at `February 28th, 2017` see the [CHANGELOG.md](CHANGELOG.md) for more details.

## Endpoints

* [Trainees](v1/trainees.md) - Manage people that can see your content
* [Groups](v1/groups.md) - List existing groups
* [Courses](v1/courses.md) - Get which courses every trainee can see
* [Catalogue](v1/catalogue.md) - Get the catalogue for a specific trainee
* [Progresses](v1/progresses.md) - Get progress status of a specific trainee

## Identify your account id and api key

To use the API you must provide your username and api_key in each request via Basic Auth.

Your username and api_key can be found in the [SABE Extend backoffice](https://backoffice.sabe-extend.com) under the menu `Administrators > Account Management`.

The api_key can be regenerated at any moment using the `regenerate` button. When this is done you will need to update all your clients with the new api_key.

## No XML, just JSON

We only support JSON for serialization of data. This means that you have to send `Content-Type: application/json; charset=utf-8` when you're POSTing, or PATCHing data into SABE Extend.

## Authentication
As stated previously, SABE Extend API uses [Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication) for authentication. All requests are secure since `https://api.sabe-extend.com` use SSL.

## Making a request

All URLs start with https://api.sabe-extend.com/v1/. SSL only. The path is prefixed with the API version. If we change the API in backward-incompatible ways, we'll bump the version number and maintain stable support for the old URLs.

To create objects using the API each request must include the `Content-Type` header with the value `application/json` and the body must contain data in the JSON format.

```shell
curl -u account_id:api_key \
  -H 'Content-Type: application/json' \
  https://api.sabe-extend.com/v1/ping
```

## Pagination

Most collection calls on our API paginate their results. The first request returns up to 25 records. Check the next page for more results by adding `&page=2`, then `&page=3`, and so on until you get an empty response.

Each page returns a maximum of 25 objects you can increase (up to 50 items per page) or decrease (to 1 per item page) this value adding the `&per_page=50`.

If a given resource is paginated the API will emit the following headers:

|  Header Name  |  Description  |
|---------------|---------------|
| X-Total: 42 | Total number of items found
| X-Total-Pages: 5 | Total number of pages
| X-Page: 3 | The current page
| X-Per-Page: 10 | Amount of items displayed per page
| X-Next-Page: 4 | The number of the next page
| X-Prev-Page: 2 | The number of the previews page
| X-Offset: 10 | The offset to start from

### Example

```shell
https://api.sabe-extend.com/v1/trainees?page=2&per_page=10
```

## Sort

SABE Extend API supports sorting for some attributes via query string. To sort a collection just add a `sort=field_name` attribute in the query string and a `-` before the `field_name` if you which descending sorting.

Each sortable entity has supported fields in its own section.

### Example

```shell
https://api.sabe-extend.com/v1/trainees?sort=-name # Sort trainees by name descending

https://api.sabe-extend.com/v1/trainees?sort=email # Sort trainees by email ascending
```

## Search

SABE Extend API supports search and filtering for some attributes via query string. To filter a collection just add a `field_name=something`. Free text searching (`q` attribute) is case insensitive.

Each searchable entity has supported fields in its own section.

### Example

```shell
https://api.sabe-extend.com/v1/groups?q=extend # returns all groups with 'extend' in name
```

## Handling errors

If SABE Extend is having trouble, you might see a 5xx error. `500` means that the app is entirely down, but you might also see `502 Bad Gateway`, `503 Service Unavailable`, or `504 Gateway Timeout`. It's your responsibility in all of these cases to retry your request later. If you try to access an unexisting URL you will get the `404 Not Found` error.
