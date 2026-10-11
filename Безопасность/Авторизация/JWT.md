
Json Web Token - вид токен который выдаётся при авторизации клиенту. Состоит из header, payload и signature - подпись, которая шзафишрована с помощью секретного ключа, который знает только сервер.

Сервер создаёт JWT, подписывая секретным ключём или приватным ключём RSA.
Клиент отправляет JWT в заголовке, сервер проверяет подпись а также claims (ресурсы куда клиент просит доступ) и в зависимости от настроек отклоняет или одобряет запрос.

##### Token Propagation

Классическая схема применения JWT такая:
1. API сервер получает из заголовка токен, который закодирован в bsse64. Он состоит из header, payload, signature
   
   Header:
   {
	"alg": "RS256", //алгоритм
	"typ": "JWT",
	"kid": "lmcnYk8LizXgo1CAFGCxuPaWKWkjKq5CI47e-mBsLpU" //открытый ключ
	}
	
	Payload:
	{
	  "exp": 1791398856,
	  "iat": 1791388056,
	  "jti": "28a21154-c926-4ba7-8acf-dda3f49338eb",
	  "iss": "https://ya-keycloak.relef.ru/realms/master",
	  "aud": "account",
	  "sub": "89ac325a-a6c9-457f-89c8-cd30bdc5f098",
	  "typ": "Bearer",
	  "azp": "chat-bot",
	  "sid": "6119a380-e69b-4de8-82fd-447d8ab17821",
	  "acr": "1",
	  "allowed-origins": [
	    "/*"
	  ],
	  "realm_access": {
	    "roles": [
	      "default-roles-master",
	      "offline_access",
	      "uma_authorization"
	    ]
	  },
	  "resource_access": {
	    "account": {
	      "roles": [
	        "manage-account",
	        "manage-account-links",
	        "view-profile"
	      ]
	    }
	  },
	  "scope": "profile user-attributes email",
	  "private_email": "ShmelkovDYU.it@ititr.ru",
	  "email_verified": false,
	  "custom_department": "Мой отдел",
	  "private_phone": "89001002233",
	  "preferred_username": "vatropin.al",
	  "given_name": "Александр",
	  "name": "Александр Ватропин",
	  "passNumber": "4b00c71197",
	  "family_name": "Ватропин",
	  "email": "webdev.it@ititr.ru"
	}

	 И signature:
	 {
	  "e": "AQAB",
	  "kty": "RSA",
	  "n": "u7QO4CvsS8xFKrbQsf2oS0C_uPoSsRWGcPMnP2maNj1addpWOHTJWM1rtVa35W2G5xn73zrUzrm6UCLjrTXApSefcL0IMP-1XK9HVPKR7E8CAOFeG7aqH_N7xvFyUkQSdIaXzA8naZ0zJoFCxrWYowSULnAmgWRlAWMCZwcJApAtsAj9HsMq_vpTBz9p64fBU0oyc15jT7qs-xgJwLcGxL2R_egzh2Ej8O4hSqCw_RBCKeW3PdoZvsGr31payL_jbPwWZ5m1OO3fUr2onNJnsaFUqnX7mwJjwLkOVbfODppVDRwsBppUbzQ_J-jyzdzF2QrQxpg83bHnDmlKPFFz-Q"
	}
	
   Задача состоит в том, чтобы проверить валидность токена. Для этого он берёт iss и получает оттуда 
   получает из него адрес issuer, идёт на него и получает публичный ключ (впоследствие кешируется), созданный ассиметричным RSA алгоритмом. 
   
2. Считает SHA-256 от header + payload и получает хэш1
3. Применяет открытый ключ к signature и получает хэш2
4. Сравнивает их. Если не равны - то токен поддельный

Если используется симметричный алгоритм HMAC, то паблик ключа нет но есть секретный ключ, который должны знать все сервиса чтобы проверить валидность токена.

Для получения нового jwt access token используется refresh token, который в отличие от access живёт долго. 

Особенность jwt access token в том, что его нельзя деактивировать на сервере, т.к. он там не хранится и не проверяется при каждом обращении, поэтому он живёт не долго. Refresh токен хранится на сервере и его можно отозвать, он живёт на порядки дольше, чем access token