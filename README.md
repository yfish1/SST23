# SST23
## Introduction 

Throughout the classes of software security & testing we learned many different security measures to secure your (web)app better. We have created a .NET website and we want to investigate how well equipped the average website is against a few common cyber attack methods.  
In this document we will discuss the different methods and see how resistant our website is against these methods. After doing an analysis we will try to introduce vulnerabilities to these attacks on these websites.  


## HTTPS 

HTTP is a protocol that is used to transmit data between a client and a server. The client requests data from the server and waits for a response from the server. HTTP is stateless meaning that there is no way of telling how requests are linked with one another. Usually this is fixed with cookies. 
HTTPS is an extension on this protocol. It uses SSL(Secure Socket Layer) to provide additional security. The SSL makes sure that all data between these parties are encrypted. 

All the domains on the Azure portals are HTTPS encrypted meaning that we also have HTTPS on our website. This makes sure that all our data both incoming and outgoing is encrypted. 

## Cross-Site Scripting (XSS) 

Cross-Site Scripting (XSS) attacks are a type of injection attack, where an attacker tries to inject maliscious scripts(javascript/HTML) into a webapp. Because it runs on the same origin as the trusted application, the malicious script can access cookies, session tokens, ….  

These scripts can change the content of the DOM, steal cookies/tokens and force users to do unwanted requests. 

XSS consists of 3 Different types :  

    Stored XSS 

    Reflected XSS 

    DOM-based XSS 

### Stored XSS (Type I) 

Stored XSS generally results to many victims being attacked. Stored XSS requires a vulnerable application that stores data into a database where data is stored and sent back to the users. This could be something like an online web form, blog, … 

The attacker sends htm/JavaScript to the application instead of normal input, this is then stored into the database and waits there. Later when the victim visits the same website, they then download this html/JavaScript. So by using the web application as a starting point, an attacker is able to attack any user. The reason why this occurs is because there is no validation for the input coming from the application going. Another reason is because there aren’t any good sanitization controls for the output coming out from of the application. 


### Reflected XSS (Type II) 

Reflected XSS can occur when a user sends something to an application and the application reflects the data right back to the same user. This means that if the users sends in some javascrip/HTML, the same script/HTML will be sent back to the same user. The problem is when for example a user tries to log in and types in the password incorrectly and he/she gets an error messages.  When the error messages that was sent back contained for example the user his/her name, someone with bad intentions can send out a phishing email to the user with a malicious string. When the user is tricked and opens the URL, the malicious string is included in the response to user. The sensitive data then gets send to the attacker and the attacker gains full access to the user his/her account. 

![alt text](https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media | width=100)

### DOM-based XSS (Type 0) 

DOM-based XSS is verry similar to reflected XSS, the only difference is that the malicious injection happens in the browser(DOM) without involving the server.  

As defined by Amit Klein, who published the first article about this issue, DOM Based XSS is a form of XSS where the entire XSS flow takes place in the browser (the DOM). It is very similar to reflected XSS, but an important difference is that the injected script is never sent to the application's server. 

For example, an attacker could inject script using a URL fragment instead of a query string: https://www.example.org/home#display=<script>alert('xss')</script>. In case other scripts in the browser use the display fragment to alter the page, this may lead to an XSS vulnerability. 

#### Attack scenario  

The following is a step-by-step description of a stored XSS attack: 

    Attacker injects script in an otherwise benevolent application; 

    Victim visits the benevolent application; 

    The attacker's script is sent to the victim's browser; 

    The attacker's script is executed in the victim's browser. 

 

Source : Attack scenario from “https://apwt.gitbook.io/software-security/injection-attacks/000introxss” 

 
## CSRF 

CSRF is a cyber attack method that stands for Cross Site Request Forgery. This method tricks the user into submitting a POST request into a different website that they think they are using. I think an example makes for a better explanation.  
Imagine if you visited a website(A) and you log in. The website returns you a cookie and everything you want. After visiting website(A) you return to website(B), a malicious website. This website takes your data from your cookie and uses that logged in session from your cookie to execute al sorts of malicious requests such as change your password. 

Usually this is fixed by storing a random string of characters and keeping that as a token. The malicious website cannot use the cookie now because the token safeguards the cookie contents. 

We have set up a XSRF token in both the cookie header and body, so we are moderately protected against CSRF attacks. The logical side is that we do not have authentication right now so there wouldn’t even be any point in executing a CSRF attack right now since anyone can make PUT/POST/DELETE requests. Below u can find an image that show the validation of Anti-Forgery Token for the DELETE action. PUT and POST both have this token as well.

 ```c#
       // POST: Movies/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> DeleteConfirmed(int id)
        {
            var movie = await _context.Movie.FindAsync(id);
            _context.Movie.Remove(movie);
            await _context.SaveChangesAsync();
            return RedirectToAction(nameof(Index));
        }
  ```

## SQLi 

 

SQLi stands for SQL injection, the attacker injects a SQL query via the input data from the client to the application. With this method, the attacker can read and modify(Insert/Update/Delete) data from the database. 

 

We make use of parameters in our project, this way the data can’t be manipulated with a SQL injection.  
