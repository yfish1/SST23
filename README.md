# SST23
## Introduction 

Throughout the classes of software security & testing we learned many different security measures to secure your (web)app better. We have created a .NET website and we want to investigate how well equipped the average website is against a few common cyber attack methods.  
In this document we will discuss the different methods and see how resistant our website is against these methods. After doing an analysis we will try to introduce vulnerabilities to these attacks on our website.  

# Make It

In this section we wil talk about the different types of attacks, what they are, how they work and how we implement it in our website.


## HTTPS 

HTTP is a protocol that is used to transmit data between a client and a server. The client requests data from the server and waits for a response from the server. HTTP is stateless meaning that there is no way of telling how requests are linked with one another. Usually this is fixed with cookies. 
HTTPS is an extension of this protocol. It uses SSL(Secure Socket Layer) to provide additional security. The SSL makes sure that all data between these parties are encrypted. 

All the domains on the Azure portals are HTTPS encrypted, meaning that we also have HTTPS on our website. This makes sure that all our data - both incoming and outgoing - is encrypted. 

![certificate](https://github.com/yfish1/SST23/blob/master/ASite.png?raw=true)

## Cross-Site Scripting (XSS) 

Cross-Site Scripting (XSS) attacks are a type of injection attack, where an attacker tries to inject maliscious scripts(javascript/HTML) into a webapp. Because it runs on the same origin as the trusted application, the malicious script can access cookies, session tokens, ….  

These scripts can change the content of the DOM(Document Object Model), steal cookies/tokens and force users to do unwanted requests. 
XSS consists of 3 Different types :  

- Stored XSS
- Reflected XSS
- DOM-based XSS


### Stored XSS (Type I) 

Stored XSS generally results to many victims being attacked. Stored XSS requires a vulnerable application that stores data into a database where data is sent back to the users. This could be something like an online web form, blog, … 

The attacker sends html/JavaScript to the application instead of normal input, this is then stored into the database and waits there. Later when the victim visits the same website, they then download this html/JavaScript. So by using the web application as a starting point, an attacker is able to attack any user. The reason why this occurs is because there is no validation for the input coming from the application going. Another reason is because there aren’t any good sanitization controls for the output coming out from of the application. 


### Reflected XSS (Type II) 

Reflected XSS can occur when a user sends data to an application and the application reflects the data right back to the user. This means that if the user sends in some JavaScript/HTML, the same script/HTML will be sent back to the same user. A problem occurs when for example, a user gets an error message when he enters the wrong password. When the error message that was sent back, contained for example the user's name. Someone with bad intentions can send out a phishing email to the user with a malicious string. When the user is tricked and opens the URL, the malicious string is included in the response to the user. The sensitive data then gets sent back to the attacker and the attacker gains full access to the user's account.  

![alt_text](https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media)
<img src="https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media" data-canonical-src="https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media" width="400" height="200" />

### DOM-based XSS (Type 0) 

DOM-based XSS is very similar to reflected XSS, the only difference is that the malicious injection happens in the browser (DOM) without involving the server. If the attack were successful, the page could possibly be changed. 

#### Attack scenario  

The following is a step-by-step description of a stored XSS attack: 

   * Attacker injects script in an otherwise benevolent application; 
   * Victim visits the benevolent application; 
   * The attacker's script is sent to the victim's browser; 
   * The attacker's script is executed in the victim's browser. 

 

Source : Attack scenario from “https://apwt.gitbook.io/software-security/injection-attacks/000introxss” 

 
## CSRF 

CSRF is a cyber attack method that stands for Cross Site Request Forgery. This method tricks the user into submitting a POST request into a different website that they think they are using. 

For instance:

Imagine if you visited a website(A) and you log in. The website returns you a cookie and everything     you want. After visiting website(A) you return to website(B), a malicious website. This website takes your data from your cookie and uses that logged in session from your cookie to execute all sorts of malicious requests such as change your password. 

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

# Break it

After our evaluation, we introduced two vulnerabilities (CSRF and XSS). With the [NAME OF SAST-TOOL], we can verify that it finds the vulnerabilities that we have introduced.


## CSRF Vulnerability

We can create a simple CSRF vulnerability by removing all the code that creates a forgery token and the code that validates a forgery token. Not using a token for your cookies makes you vulnerable against CSRF. The authentication on our website would store a cookie which the attacker can now use to his advantage. Of course, the user would still have to be lured to the malicious site first. 


## HTTPS Vulnerability

We tried to disable HTTPS and make our website not use an SSL however this is something that is configured centrally within azure. At most you can disable redirection to HTTPS and lower your TLS version but further than that Azure forces you to at least use HTTPS. In other cloud services this is also the case, AWS does also not compromise on this. 


## XSS

Geprobeerd maar geen cookies/token (uitbreiden tekst) 


## SQLi

NOG TE DOEN

