## JwtArgonaut object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import scala.util.Try
import scala.util.Try

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim}

scala> import argonaut.Json
import argonaut.Json

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now().plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@ea5cfec7

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3Njg5MzMwNDYsImlhdCI6MTYxMTE0ODI4Nn0.zUea_ZxyEnlW07zgeNZXO0uR1lz7JUMw6bHJBdJP-3P6DKN92UaWvK638sUtpWGwk0SPQDKqbTQjdYv0bvNR9Q

scala> val decodedJson: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJson: scala.util.Try[argonaut.Json] = Success({"exp":1768933046,"iat":1611148286})

scala> val decodedClaim: Try[JwtClaim] = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@ea5cfec7)
```

### Encoding

```scala
scala> import argonaut.Parse
import argonaut.Parse

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut}

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val jsonClaim = Parse.parseOption(s"""{"expires":${Instant.now().getEpochSecond}}""").get
jsonClaim: argonaut.Json = {"expires":1611148286}

scala> val jsonHeader = Parse.parseOption("""{"typ":"JWT","alg":"HS512"}""").get
jsonHeader: argonaut.Json = {"typ":"JWT","alg":"HS512"}

scala> val token1: String = JwtArgonaut.encode(jsonClaim)
token1: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNjExMTQ4Mjg2fQ.

scala> val token2: String = JwtArgonaut.encode(jsonClaim, key, alg)
token2: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNjExMTQ4Mjg2fQ.NxJgG_7rxAur60OXIjjj34y732lvokIWNVYlqqzGcSCJZ75tM6KyYNov27NjqYjwyWhYjmuoX24ynSppgh9z9g

scala> val token3: String = JwtArgonaut.encode(jsonHeader, jsonClaim, key)
token3: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHBpcmVzIjoxNjExMTQ4Mjg2fQ.NxJgG_7rxAur60OXIjjj34y732lvokIWNVYlqqzGcSCJZ75tM6KyYNov27NjqYjwyWhYjmuoX24ynSppgh9z9g
```

### Decoding

```scala
scala> import scala.util.Try
import scala.util.Try

scala> import argonaut.Json
import argonaut.Json

scala> import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim, JwtHeader}
import pdi.jwt.{JwtAlgorithm, JwtArgonaut, JwtClaim, JwtHeader}

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@ba7651a8

scala> val key = "secretKey"
key: String = secretKey

scala> val alg = JwtAlgorithm.HS512
alg: pdi.jwt.JwtAlgorithm.HS512.type = HS512

scala> val token = JwtArgonaut.encode(claim, key, alg)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJleHAiOjE3Njg5MzMwNDcsImlhdCI6MTYxMTE0ODI4N30.jFFz7AMLR7mi9nNfiVdp3pCpvM-B9qh3m3CTfNip7RMcp6w8R0I8PahBxi8mx6kVU0nUjsIxyd6TbjX5KeA4Ww

scala> val decodedJsonClaim: Try[Json] = JwtArgonaut.decodeJson(token, key, Seq(alg))
decodedJsonClaim: scala.util.Try[argonaut.Json] = Success({"exp":1768933047,"iat":1611148287})

scala> val decodedJson: Try[(Json, Json, String)] = JwtArgonaut.decodeJsonAll(token, key, Seq(alg))
decodedJson: scala.util.Try[(argonaut.Json, argonaut.Json, String)] = Success(({"typ":"JWT","alg":"HS512"},{"exp":1768933047,"iat":1611148287},jFFz7AMLR7mi9nNfiVdp3pCpvM-B9qh3m3CTfNip7RMcp6w8R0I8PahBxi8mx6kVU0nUjsIxyd6TbjX5KeA4Ww))

scala> val decodedClaim: Try[JwtClaim]  = JwtArgonaut.decode(token, key, Seq(alg))
decodedClaim: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@ba7651a8)

scala> val decodedToken: Try[(JwtHeader, JwtClaim, String)] = JwtArgonaut.decodeAll(token, key, Seq(alg))
decodedToken: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@7e1481b1,pdi.jwt.JwtClaim@ba7651a8,jFFz7AMLR7mi9nNfiVdp3pCpvM-B9qh3m3CTfNip7RMcp6w8R0I8PahBxi8mx6kVU0nUjsIxyd6TbjX5KeA4Ww))
```
