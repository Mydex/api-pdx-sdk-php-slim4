# php-base - PHP Slim 4 Server library for PDX API

* [OpenAPI Generator](https://openapi-generator.tech)
* [Slim 4 Documentation](https://www.slimframework.com/docs/v4/)

This server has been generated with [Slim PSR-7](https://github.com/slimphp/Slim-Psr7) implementation.
[PHP-DI](https://php-di.org/doc/frameworks/slim.html) package used as dependency container.

## Requirements

* Web server with URL rewriting
* PHP 8.1 or newer

This package contains `.htaccess` for Apache configuration.
If you use another server(Nginx, HHVM, IIS, lighttpd) check out [Web Servers](https://www.slimframework.com/docs/v3/start/web-servers.html) doc.

## Installation via [Composer](https://getcomposer.org/)

Navigate into your project's root directory and execute the bash command shown below.
This command downloads the Slim Framework and its third-party dependencies into your project's `vendor/` directory.
```bash
$ composer install
```

## Add configs

[PHP-DI package](https://php-di.org/doc/getting-started.html) helps to decouple configuration from implementation. App loads configuration files in straight order(`$env` can be `prod` or `dev`):
1. `config/$env/default.inc.php` (contains safe values, can be committed to vcs)
2. `config/$env/config.inc.php` (user config, excluded from vcs, can contain sensitive values, passwords etc.)
3. `lib/App/RegisterDependencies.php`

## Start devserver

Run the following command in terminal to start localhost web server, assuming `./php-slim-server/public/` is public-accessible directory with `index.php` file:
```bash
$ php -S localhost:8888 -t php-slim-server/public
```
> **Warning** This web server was designed to aid application development.
> It may also be useful for testing purposes or for application demonstrations that are run in controlled environments.
> It is not intended to be a full-featured web server. It should not be used on a public network.

## Tests

### PHPUnit

This package uses PHPUnit 8 or 9(depends from your PHP version) for unit testing.
[Test folder](tests) contains templates which you can fill with real test assertions.
How to write tests read at [2. Writing Tests for PHPUnit - PHPUnit 8.5 Manual](https://phpunit.readthedocs.io/en/8.5/writing-tests-for-phpunit.html).

#### Run

Command | Target
---- | ----
`$ composer test` | All tests
`$ composer test-apis` | Apis tests
`$ composer test-models` | Models tests

#### Config

Package contains fully functional config `./phpunit.xml.dist` file. Create `./phpunit.xml` in root folder to override it.

Quote from [3. The Command-Line Test Runner — PHPUnit 8.5 Manual](https://phpunit.readthedocs.io/en/8.5/textui.html#command-line-options):

> If phpunit.xml or phpunit.xml.dist (in that order) exist in the current working directory and --configuration is not used, the configuration will be automatically read from that file.

### PHP CodeSniffer

[PHP CodeSniffer Documentation](https://github.com/squizlabs/PHP_CodeSniffer/wiki). This tool helps to follow coding style and avoid common PHP coding mistakes.

#### Run

```bash
$ composer phpcs
```

#### Config

Package contains fully functional config `./phpcs.xml.dist` file. It checks source code against PSR-1 and PSR-2 coding standards.
Create `./phpcs.xml` in root folder to override it. More info at [Using a Default Configuration File](https://github.com/squizlabs/PHP_CodeSniffer/wiki/Advanced-Usage#using-a-default-configuration-file)

### PHPLint

[PHPLint Documentation](https://github.com/overtrue/phplint). Checks PHP syntax only.

#### Run

```bash
$ composer phplint
```

## Show errors

Switch your app environment to development
- When using with some webserver => in `public/.htaccess` file:
```ini
## .htaccess
<IfModule mod_env.c>
    SetEnv APP_ENV 'development'
</IfModule>
```

- Or when using whatever else, set `APP_ENV` environment variable like this:
```bash
export APP_ENV=development
```
or simply
```bash
export APP_ENV=dev
```

## Mock Server
Since this feature should be used for development only, change environment to `development` and send additional HTTP header `X-Mydex\ApiPdxSdk\Slim4-Mock: ping` with any request to get mocked response.
CURL example:
```console
curl --request GET \
    --url 'http://localhost:8888/v2/pet/findByStatus?status=available' \
    --header 'accept: application/json' \
    --header 'X-Mydex\ApiPdxSdk\Slim4-Mock: ping'
[{"id":-8738629417578509312,"category":{"id":-4162503862215270400,"name":"Lorem ipsum dol"},"name":"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem i","photoUrls":["Lor"],"tags":[{"id":-3506202845849391104,"name":"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Lorem ipsum dolor sit amet, consectet"}],"status":"pending"}]
```

Used packages:
* [Openapi Data Mocker](https://github.com/ybelenko/openapi-data-mocker) - first implementation of OAS3 fake data generator.
* [Openapi Data Mocker Server Middleware](https://github.com/ybelenko/openapi-data-mocker-server-middleware) - PSR-15 HTTP server middleware.
* [Openapi Data Mocker Interfaces](https://github.com/ybelenko/openapi-data-mocker-interfaces) - package with mocking interfaces.

## Logging

Build contains pre-configured [`monolog/monolog`](https://github.com/Seldaek/monolog) package. Make sure that `logs` folder is writable.
Add required log handlers/processors/formatters in `lib/App/RegisterDependencies.php`.

## API Endpoints

All URIs are relative to *https://api.mydex.org*

> Important! Do not modify abstract API controllers directly! Instead extend them by implementation classes like:

```php
// src/Api/PetApi.php

namespace Mydex\ApiPdxSdk\Slim4\Api;

use Mydex\ApiPdxSdk\Slim4\Api\AbstractPetApi;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Message\ResponseInterface;

class PetApi extends AbstractPetApi
{
    public function addPet(
        ServerRequestInterface $request,
        ResponseInterface $response
    ): ResponseInterface {
        // your implementation of addPet method here
    }
}
```

When you need to inject dependencies into API controller check [PHP-DI - Controllers as services](https://github.com/PHP-DI/Slim-Bridge#controllers-as-services) guide.

Place all your implementation classes in `./src` folder accordingly.
For instance, when abstract class located at `./lib/Api/AbstractPetApi.php` you need to create implementation class at `./src/Api/PetApi.php`.

Class | Method | HTTP request | Description
------------ | ------------- | ------------- | -------------
*AbstractCalendarApi* | **deleteCalendarEvent** | **DELETE** /calendar/delete-event | Supports deleting a calendar-event.
*AbstractCalendarApi* | **getCalendarAppointments** | **GET** /calendar/get-appointments | Retrieve appointments for a given context
*AbstractCalendarApi* | **getCalendarEvents** | **GET** /calendar/get-events | Retrieve calendar events
*AbstractCalendarApi* | **postCalendarAppointment** | **POST** /calendar/add-appointment | Supports adding a calendar-appointment.
*AbstractCalendarApi* | **postCalendarEvent** | **POST** /calendar/add-event | Supports adding a calendar-event.
*AbstractCalendarApi* | **putCalendarEvent** | **PUT** /calendar/update-event | Supports updating a calendar-event.
*AbstractReferralsApi* | **getAllReferrals** | **GET** /referrals/read-all | Retrieve rall referrals for a given member.
*AbstractReferralsApi* | **getSingleReferral** | **GET** /referrals/read-single | Retrieve a single referral by id for a given member.
*AbstractReferralsApi* | **postSelfReferral** | **POST** /referrals/self-refer | Supports self-referral.
*AbstractReferralsApi* | **updateReferralStatus** | **PUT** /referrals/update-status | Supports updating the status of a referral.
*AbstractSecureMessagesApi* | **getSecureMessageConversation** | **GET** /secure-messages/conversation | Retrieve all secure messages for a given conversation.
*AbstractSecureMessagesApi* | **postSecureMessageReceive** | **POST** /secure-messages/receive | Supports sending a message to the member's PDS.
*AbstractSecureMessagesApi* | **postSecureMessageSend** | **POST** /secure-messages/send | Supports sending a message from the member's PDS.
*AbstractTimelineApi* | **getTimeline** | **GET** /timeline | Retrieve list of timeline items.
*AbstractTimelineApi* | **getTimelineItem** | **GET** /timeline/single-item | Retrieve a single timeline item.


## Models

* Mydex\ApiPdxSdk\Slim4\Model\AuthErrorResponse
* Mydex\ApiPdxSdk\Slim4\Model\AuthErrorResponseError
* Mydex\ApiPdxSdk\Slim4\Model\CalendarAppointmentCreateRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\CalendarEvent
* Mydex\ApiPdxSdk\Slim4\Model\CalendarEventCommonRequestBodyFields
* Mydex\ApiPdxSdk\Slim4\Model\CalendarEventCreateRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\CalendarEventDeleteRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\CalendarEventUpdateRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\CreateResponse
* Mydex\ApiPdxSdk\Slim4\Model\DeleteResponse
* Mydex\ApiPdxSdk\Slim4\Model\ErrorResponse
* Mydex\ApiPdxSdk\Slim4\Model\GetAppointmentsResponse
* Mydex\ApiPdxSdk\Slim4\Model\GetEventsResponse
* Mydex\ApiPdxSdk\Slim4\Model\ReferralCommonRequestBodyFields
* Mydex\ApiPdxSdk\Slim4\Model\ReferralCommonRequestBodyFieldsReferreeAvailability
* Mydex\ApiPdxSdk\Slim4\Model\ReferralListResponse
* Mydex\ApiPdxSdk\Slim4\Model\ReferralListResponseReferralsInner
* Mydex\ApiPdxSdk\Slim4\Model\ReferralSingleResponse
* Mydex\ApiPdxSdk\Slim4\Model\ReferralSingleResponseReferral
* Mydex\ApiPdxSdk\Slim4\Model\ReferralStatusUpdateRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\SecureMessage
* Mydex\ApiPdxSdk\Slim4\Model\SecureMessageCommonFields
* Mydex\ApiPdxSdk\Slim4\Model\SecureMessageConversationResponse
* Mydex\ApiPdxSdk\Slim4\Model\SecureMessageReceiveRequest
* Mydex\ApiPdxSdk\Slim4\Model\SecureMessageSendRequest
* Mydex\ApiPdxSdk\Slim4\Model\SelfReferralRequestBody
* Mydex\ApiPdxSdk\Slim4\Model\TimelineItemCalendarEvent
* Mydex\ApiPdxSdk\Slim4\Model\TimelineItemReferral
* Mydex\ApiPdxSdk\Slim4\Model\TimelineItemSecureMessage
* Mydex\ApiPdxSdk\Slim4\Model\TimelineResponse
* Mydex\ApiPdxSdk\Slim4\Model\TimelineResponseTimelineInner
* Mydex\ApiPdxSdk\Slim4\Model\TimelineResponseTimelineInnerOneOf
* Mydex\ApiPdxSdk\Slim4\Model\TimelineResponseTimelineInnerOneOf1
* Mydex\ApiPdxSdk\Slim4\Model\TimelineResponseTimelineInnerOneOf2
* Mydex\ApiPdxSdk\Slim4\Model\TimelineSingleItemResponse
* Mydex\ApiPdxSdk\Slim4\Model\TimelineSingleItemResponseTimelineItem
* Mydex\ApiPdxSdk\Slim4\Model\UpdateResponse


## Authentication

### Security schema `oauth2`
> Important! To make OAuth authentication work you need to extend [\Mydex\ApiPdxSdk\Slim4\Auth\AbstractAuthenticator](./lib/Auth/AbstractAuthenticator.php) class by [\Mydex\ApiPdxSdk\Slim4\Auth\OAuthAuthenticator](./src/Auth/OAuthAuthenticator.php) class.

Scope list:
* `mydex:pdx` - Global access scope
* `post:/calendar/add-event` - Create a calendar event
* `post:/calendar/add-appointment` - Create a calendar appointment
* `put:/calendar/update-event` - Update a calendar event
* `delete:/calendar/delete-event` - Delete a calendar event
* `get:/calendar/get-events` - Read calendar events
* `get:/calendar/get-appointments` - Read calendar appointments
* `post:/referrals/self-refer` - Make a self-referral to a service
* `get:/referrals/read-all` - Read all referrals
* `get:/referrals/read-single` - Read a single referral
* `put:/referrals/update-status` - Update the status of a referral
* `post:/secure-messages/send` - Send a message FROM the member&#39;s PDS
* `post:/secure-messages/receive` - Send a message TO the member&#39;s PDS
* `get:/secure-messages/conversation` - Read messages grouped by the specified conversation
* `get:/timeline` - Read a list of timeline items
* `get:/timeline/single-item` - Read a single item from the timeline

### Advanced middleware configuration
Ref to used Slim Token Middleware [dyorg/slim-token-authentication](https://github.com/dyorg/slim-token-authentication/tree/1.x#readme)
