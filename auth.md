> Initially keep information in chunks , later organize it in seamless way

> # What are JWTs?   
> JSON data + Signature (cryptographically signed) = JWT


> # Stateless JWT   
> A JWT that is entirely self-contained and holds all user information, necessary to complete   
> a transaction within it. eg: username, email, firstName, lastName etc.

> # Statful JWT   
> A JWT that only contains a sessionId. All User Data is stored server side and retrieved from a database.

> # Session Tokens
> A cryptographically signed (or unsigned) session identifier. All user data is stored server side
> and retrieved from a database

> # Cookies  
> An http header field, that allows you to store or retrieve   
> key/value data, set data expiration times, and apply various   
> other data integrity rules. Caps out at ~4K.

> # Creating Cookies
```json
// WEB_SERVER will set this in response headers
{
 "Set-Cookie": "session=12345"
}
```
> # Reading Cookies
```json
// WEB_SERVER will read this in REQUEST HEADER
{
 "Cookie": "session=12345"
}
```


> # CSRF
>  with cookies you are susceptible to CSRF attacks   
>  with local-storage you are safe from CSRF, but opened yourself upto a much greater attack vector XSS.   
>  CSRF is trivial to fix, XSS not so much.


> # What is **Auth**
1. Authentication
2. Authorization

> # Where does auth happen?   
> The SERVER (on requests)   

> # What about the clients?   
> Clients make REQUESTS!   


> # Getting Authenticated Flow

> 1. User opens page for the first time
> 2. User signs in (Fires request to SERVER, that returns secure COOKIE)
> 3. Client stores that secure COOKIE (in a way that can't be accessed by your javascript)
> 4. From here on, ALL REQUESTS to the SERVER will include that COOKIE.


