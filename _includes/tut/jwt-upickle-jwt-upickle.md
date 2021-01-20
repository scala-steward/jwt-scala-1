## JwtUpickle Object

### Basic usage

```scala
scala> import java.time.Instant
import java.time.Instant

scala> import upickle.default._
import upickle.default._

scala> import pdi.jwt.{JwtUpickle, JwtAlgorithm, JwtClaim}
import pdi.jwt.{JwtUpickle, JwtAlgorithm, JwtClaim}

scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@e82c7e4d

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3Njg5MjczNTUsImlhdCI6MTYxMTE0MjU5NX0.tVmEVzI29QCnpC8yNf6qZugUpM5ESVfylI13DTw_cNQ

scala> JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res0: scala.util.Try[ujson.Value] = Success({"exp":1768927355,"iat":1611142595})

scala> JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res1: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@e82c7e4d)
```

### Encoding

```scala
scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val claimJson = read[ujson.Value](s"""{"expires":${Instant.now.getEpochSecond}}""")
claimJson: ujson.Value = {"expires":1611142595}

scala> val header = read[ujson.Value]( """{"typ":"JWT","alg":"HS256"}""")
header: ujson.Value = {"typ":"JWT","alg":"HS256"}

scala> // From just the claim to all possible attributes
     | JwtUpickle.encode(claimJson)
res3: String = eyJhbGciOiJub25lIn0.eyJleHBpcmVzIjoxNjExMTQyNTk1fQ.

scala> JwtUpickle.encode(claimJson, key, algo)
res4: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNjExMTQyNTk1fQ.snPU6pgWIz7_f_4FRdC_Z7SfqrfH9cmEviZsxZYMpgk

scala> JwtUpickle.encode(header, claimJson, key)
res5: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHBpcmVzIjoxNjExMTQyNTk1fQ.snPU6pgWIz7_f_4FRdC_Z7SfqrfH9cmEviZsxZYMpgk
```

### Decoding

```scala
scala> val claim = JwtClaim(
     |   expiration = Some(Instant.now.plusSeconds(157784760).getEpochSecond),
     |   issuedAt = Some(Instant.now.getEpochSecond)
     | )
claim: pdi.jwt.JwtClaim = pdi.jwt.JwtClaim@4f723d38

scala> val key = "secretKey"
key: String = secretKey

scala> val algo = JwtAlgorithm.HS256
algo: pdi.jwt.JwtAlgorithm.HS256.type = HS256

scala> val token = JwtUpickle.encode(claim, key, algo)
token: String = eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3Njg5MjczNTYsImlhdCI6MTYxMTE0MjU5Nn0.kVBgYDZhKM8p3XIzJxW4fFhByJiyRrHCTy6RcfD8FXk

scala> // You can decode to JsObject
     | JwtUpickle.decodeJson(token, key, Seq(JwtAlgorithm.HS256))
res7: scala.util.Try[ujson.Value] = Success({"exp":1768927356,"iat":1611142596})

scala> JwtUpickle.decodeJsonAll(token, key, Seq(JwtAlgorithm.HS256))
res8: scala.util.Try[(ujson.Value, ujson.Value, String)] = Success(({"typ":"JWT","alg":"HS256"},{"exp":1768927356,"iat":1611142596},kVBgYDZhKM8p3XIzJxW4fFhByJiyRrHCTy6RcfD8FXk))

scala> // Or to case classes
     | JwtUpickle.decode(token, key, Seq(JwtAlgorithm.HS256))
res10: scala.util.Try[pdi.jwt.JwtClaim] = Success(pdi.jwt.JwtClaim@4f723d38)

scala> JwtUpickle.decodeAll(token, key, Seq(JwtAlgorithm.HS256))
res11: scala.util.Try[(pdi.jwt.JwtHeader, pdi.jwt.JwtClaim, String)] = Success((pdi.jwt.JwtHeader@ac020068,pdi.jwt.JwtClaim@4f723d38,kVBgYDZhKM8p3XIzJxW4fFhByJiyRrHCTy6RcfD8FXk))
```
