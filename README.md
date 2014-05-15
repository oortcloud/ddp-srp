ddp-srp
=======

[![Build Status](https://travis-ci.org/oortcloud/ddp-srp.svg)](https://travis-ci.org/oortcloud/ddp-srp)

Library for Secure Remote Password (SRP) exchanges.


###`SRP.generateVerifier(password, options)`

Generate a new SRP verifier. Password is the plaintext password.

`options` is optional and can include:

- `identity`: `String`. The SRP username to user. Mostly this is passed in for testing.  Random UUID if not provided.
- `salt`: `String`. A salt to use.  Mostly this is passed in for testing.  Random UUID if not provided.
- SRP parameters (see _defaults and paramsFromOptions below)


###`SRP.Client`

Generate a new SRP client object. Password is the plaintext password.

`options` is optional and can include:

- `a`: client's private ephemeral value. `String` or BigInteger. Normally, this is picked randomly, but it can be passed in for testing.
- SRP parameters (see _defaults and paramsFromOptions below)


###`SRP.Client.startExchange()`

Initiate an SRP exchange.

returns `{ A: 'client public ephemeral key. hex encoded integer.' }`


###`SRP.Client.prototype.respondToChallenge(challenge)`

Respond to the server's challenge with a proof of password.

challenge is an object with

- `B`: server public ephemeral key. hex encoded integer.
- `identity`: user's identity (SRP username).
- `salt`: user's salt.

returns `{ M: 'client proof of password. hex encoded integer.' }`

throws an error if it got an invalid challenge.


###`SRP.Client.verifyConfirmation(confirmation)`

Verify server's confirmation message.

confirmation is an object with
- `HAMK`: server's proof of password.

returns true or false.


###`SRP.Server(verifier, options)`

Generate a new SRP server object.

options is optional and can include:

- `b`: server's private ephemeral value. `String` or `BigInteger`. Normally, this is picked randomly, but it can be passed in for testing.
- SRP parameters (see _defaults and paramsFromOptions below)


###`SRP.Server.issueChallenge(request)`

Issue a challenge to the client.

Takes a request from the client containing:

- `A`: hex encoded int.

Returns a challenge with:

- `B`: server public ephemeral key. hex encoded integer.
- `identity`: user's identity (SRP username).
- `salt`: user's salt.

Throws an error if issued a bad request.



###`SRP.Server.verifyResponse(response)`

Verify a response from the client and return confirmation.

Takes a challenge response from the client containing:

- `M`: client proof of password. hex encoded int.

Returns a confirmation if the client's proof is good:

- `HAMK`: server proof of password. hex encoded integer.
 OR `null` if the client's proof doesn't match.


###Default paramters

```js
/**
 * Default parameter values for SRP.
 *
 */
var _defaults = {
  hash: function (x) { return SHA256(x).toLowerCase(); },
  N: new BigInteger("EEAF0AB9ADB38DD69C33F80AFA8FC5E86072618775FF3C0B9EA2314C9C256576D674DF7496EA81D3383B4813D692C6E0E0D5D8E250B98BE48E495C1D6089DAD15DC7D7B46154D6B6CE8EF4AD69B15D4982559B297BCF1885C529F566660E57EC68EDBC3C05726CC02FD4CBF4976EAA9AFD5138FE8376435B9FC61D2FC0EB06E3", 16),
  g: new BigInteger("2")
};
_defaults.k = new BigInteger(
  _defaults.hash(
    _defaults.N.toString(16) +
      _defaults.g.toString(16)),
  16);
```


