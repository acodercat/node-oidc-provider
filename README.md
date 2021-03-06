# oidc-provider

[![build][travis-image]][travis-url] [![codecov][codecov-image]][codecov-url]

oidc-provider is an OpenID Provider implementation of [OpenID Connect][openid-connect]. It allows to
export a complete mountable or standalone OpenID Provider implementation. This implementation does
not dictate a fixed data model or persistence store, instead, you must provide adapters for these.
A generic in-memory adapter is available to get you started as well as feature-less dev-only views
to be able to get off the ground.

**NOTICE:** oidc-provider ^4.0.0 drops support for Node.js versions less than lts/carbon(8.9.0) and
introduces various breaking changes. See the CHANGELOG for a complete list of deprecations and changes.

**Table of Contents**

<!-- TOC depthFrom:2 depthTo:3 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Implemented specs & features](#implemented-specs--features)
- [Certification](#certification)
- [Get started](#get-started)
- [Configuration and Initialization](#configuration-and-initialization)
- [Debugging](#debugging)
- [Events](#events)

<!-- /TOC -->

## Implemented specs & features

The following specifications are implemented by oidc-provider. Note that not all features are
enabled by default, check the configuration section on how to enable them.

- [OpenID Connect Core 1.0][core]
  - Authorization (Authorization Code Flow, Implicit Flow, Hybrid Flow)
  - UserInfo Endpoint and ID Tokens including Signing and Encryption
  - Passing a Request Object by Value or Reference including Signing and Encryption
  - Public and Pairwise Subject Identifier Types
  - Offline Access / Refresh Token Grant
  - Client Credentials Grant
  - Client Authentication incl. client_secret_jwt and private_key_jwt methods
- [OpenID Connect Discovery 1.0][discovery]
- [OpenID Connect Dynamic Client Registration 1.0][registration]
- [OAuth 2.0 Form Post Response Mode][form-post]
- [RFC7636 - Proof Key for Code Exchange by OAuth Public Clients][pkce]
- [RFC7009 - OAuth 2.0 Token Revocation][revocation]
- [RFC7662 - OAuth 2.0 Token Introspection][introspection]
- [RFC8252 - OAuth 2.0 for Native Apps BCP][oauth-native-apps]

The following drafts/experimental specifications are implemented by oidc-provider.
- [OpenID Connect Session Management 1.0 - draft 28][session-management]
- [OpenID Connect Back-Channel Logout 1.0 - draft 04][backchannel-logout]
- [OpenID Connect Front-Channel Logout 1.0 - draft 02][frontchannel-logout]
- [RFC7592 - OAuth 2.0 Dynamic Client Registration Management Protocol (Update and Delete)][registration-management]
- [OAuth 2.0 Web Message Response Mode - draft 00][wmrm]
- [OAuth 2.0 Device Flow for Browserless and Input Constrained Devices - draft 11][device-flow]
- [JWT Response for OAuth Token Introspection - draft 01][jwt-introspection]

Updates to draft and experimental specification versions are released as MINOR library versions,
if you utilize these specification implementations consider using the tilde `~` operator in your
package.json since breaking changes may be introduced as part of these version updates.


## Certification
[<img width="184" height="96" align="right" src="https://cdn.rawgit.com/panva/node-oidc-provider/acd3ebf2/OpenID_Certified.png" alt="OpenID Certification">][openid-certified-link]  
Filip Skokan has [certified][openid-certified-link] that [oidc-provider][npm-url]
conforms to the OP Basic, OP Implicit, OP Hybrid, OP Config, OP Dynamic and OP Form Post profiles
of the OpenID Connect™ protocol.

[![build][conformance-image]][conformance-url]


<h2>Sponsor</h2>
<table>
  <tbody>
    <tr>
      <td>
        <img alt="auth0-logo" src="https://avatars.githubusercontent.com/u/2824157?s=75&v=4" style="max-width:100%;">
      </td>
      <td colspan="2">
        If you want to quickly add OpenID Connect authentication to Node.js apps, feel free to check out Auth0's Node.js SDK and free plan at <a href="https://auth0.com/overview?utm_source=GHsponsor&utm_medium=GHsponsor&utm_campaign=oidc-provider&utm_content=auth">auth0.com/overview</a>.
      </td>
    </tr>
  </tbody>
</table>


## Get started
You may follow an example [step by step setup][example-repo] (recommended), or run and experiment with an
example server that's part of the repo (if you can follow the structure, if not check the step by step).

The example bundled in this repo's codebase is available for you to experiment with [here][heroku-example].
Dynamic Registration is open, you can literally register any
client you want there.  
An example client using this provider is available [here][heroku-example-client]
(uses [openid-client][openid-client]).


## Configuration and Initialization
oidc-provider allows to be extended and configured in various ways to fit a variety of uses. See
the [available configuration](/docs/configuration.md).

```js
const Provider = require('oidc-provider');
const configuration = {
  // ... see available options /docs/configuration.md
};
const clients = [{
  client_id: 'foo',
  client_secret: 'bar',
  redirect_uris: ['http://lvh.me:8080/cb'],
  // + other client properties
}];

const oidc = new Provider('http://localhost:3000', configuration);

(async () => {
  await oidc.initialize({ clients });
  // oidc.callback => express/nodejs style application callback (req, res)
  // oidc.app => koa2.x application
  oidc.listen(3000);
  console.log('oidc-provider listening on port 3000, check http://localhost:3000/.well-known/openid-configuration');
})().catch((err) => {
  console.error(err);
  process.exitCode = 1;
});
```


## Debugging
oidc-provider uses the [debug][debug-link] module internally to log information about various states
of authentication requests, errors and grants. To see all these set the `DEBUG` environment variable
to `oidc-provider:*` when launching your app.

There is no filter on what is included in the debug output, since it may end-user Personally
identifiable information or client credentials it's use is only advised for debugging, not regular
logging. Use emitted events to cherry pick the one's of interest to your flows and form your own
logs aware of what should and should not be a part of a logged message.


## Events
Your oidc-provider instance is an event emitter, using event handlers you can hook into the various
actions and i.e. emit metrics or that react to specific triggers. In some scenarios you can even
change the defined behavior.  
See the list of available emitted [event names](/docs/events.md) and their description.


[travis-image]: https://api.travis-ci.com/panva/node-oidc-provider.svg?branch=master
[travis-url]: https://travis-ci.com/panva/node-oidc-provider
[conformance-image]: https://api.travis-ci.com/panva/oidc-provider-conformance-tests.svg?branch=master
[conformance-url]: https://github.com/panva/oidc-provider-conformance-tests
[codecov-image]: https://img.shields.io/codecov/c/github/panva/node-oidc-provider/master.svg
[codecov-url]: https://codecov.io/gh/panva/node-oidc-provider
[npm-url]: https://www.npmjs.com/package/oidc-provider
[openid-certified-link]: https://openid.net/certification/
[openid-connect]: https://openid.net/connect/
[core]: https://openid.net/specs/openid-connect-core-1_0.html
[discovery]: https://openid.net/specs/openid-connect-discovery-1_0.html
[registration]: https://openid.net/specs/openid-connect-registration-1_0.html
[session-management]: https://openid.net/specs/openid-connect-session-1_0-28.html
[form-post]: https://openid.net/specs/oauth-v2-form-post-response-mode-1_0.html
[revocation]: https://tools.ietf.org/html/rfc7009
[introspection]: https://tools.ietf.org/html/rfc7662
[pkce]: https://tools.ietf.org/html/rfc7636
[node-jose]: https://github.com/cisco/node-jose
[example-repo]: https://github.com/panva/node-oidc-provider-example
[heroku-example]: https://guarded-cliffs-8635.herokuapp.com/.well-known/openid-configuration
[heroku-example-client]: https://tranquil-reef-95185.herokuapp.com/client
[openid-client]: https://github.com/panva/node-openid-client
[backchannel-logout]: https://openid.net/specs/openid-connect-backchannel-1_0-04.html
[frontchannel-logout]: https://openid.net/specs/openid-connect-frontchannel-1_0-02.html
[registration-management]: https://tools.ietf.org/html/rfc7592
[oauth-native-apps]: https://tools.ietf.org/html/rfc8252
[debug-link]: https://github.com/visionmedia/debug
[wmrm]: https://tools.ietf.org/html/draft-sakimura-oauth-wmrm-00
[device-flow]: https://tools.ietf.org/html/draft-ietf-oauth-device-flow-11
[jwt-introspection]: https://tools.ietf.org/html/draft-lodderstedt-oauth-jwt-introspection-response-01
