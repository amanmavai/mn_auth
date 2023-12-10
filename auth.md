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



> # SPA vs SSR (Remix)
> **SPA**: In a Vite with React setup, the entire JavaScript bundle is typically loaded by the client's browser upon the initial visit,   
> regardless of the user's authentication status. This means that all the code related to the application, including routes and components
> that might only be relevant post-authentication, is available to the client.

> **SPA**: During the login flow, even though the user has not authenticated yet, they have access to the entire application's code.   
> The **actual protection** of data and restricted actions relies on API-level security   
> (i.e., the server not providing sensitive data or performing actions without valid authentication).

> **SPA**: However, as long as sensitive data and actions are protected server-side, this exposure does not inherently lead to a security vulnerability,   
> but rather to a **potential disclosure of application structure and logic**.   

> **SSR**: **Selective Bundle Loading**: Remix adopts a different approach. It can selectively load resources based on the request context.   
> Since it supports server-side rendering (SSR), the initial HTML sent to the client can be tailored based on the user's authentication status.

> **SSR**: Offers more control over what is sent to the client, enhancing security and potentially improving load performance.   
> Ideal for applications where the exposure of post-login application structure and logic is a concern.   

> **SPA**: Implement code splitting to load sensitive parts only when needed. However, understand that this does not prevent a determined user from accessing these chunks.
> Use code splitting to separate the JavaScript bundle into smaller chunks. This prevents loading all scripts upfront.   
> Implement route-based code splitting so that the code for authenticated routes is loaded only when those routes are accessed.   


> **SPA**: For someone who is not authenticated to find out the URL of a private chunk in a Vite with React application,   
> they would typically have to rely on indirect methods, as they wouldn't have direct access to the network requests   
> that load these chunks post-authentication. Here's how they might approach it
> Guesswork Based on File Naming Conventions   
> The initial JavaScript bundle loaded by the application may contain references to other chunks, such as dynamic import statements.

