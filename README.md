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

### DOM-based XSS (Type III) 

DOM-based XSS is very similar to reflected XSS, the only difference is that the malicious injection happens in the browser (DOM) without involving the server. If the attack were successful, the page could possibly be changed. 

<img src="https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media" data-canonical-src="https://gblobscdn.gitbook.com/assets%2F-M-ZWft_B1sfuGrGj7RW%2Fsync%2Fd09ebf15bc8731335dca54e085b1dfd135f82824.png?alt=media" width="668" height="376" />

[source](https://apwt.gitbook.io/software-security/injection-attacks/000introxss)

 
## CSRF 

CSRF is a cyber attack method that stands for Cross Site Request Forgery. This method tricks the user into submitting a POST request into a different website that they think they are using. 

**For instance:**
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

 

SQLi stands for SQL injection, as the name says it, it injects an unwanted SQL command into your web app. When you have any sort of input that makes a request to your SQL database, your database will return whatever the request asks for. Using this input the user can manipulate this by sending a malicious request to your SQL-database by for example making a query to get a password that is stored in that database.  
You can **defend against SQLi** with these **4** methods: 

    * Using prepared statements 
    * Using Stored Procedures 
    * Using Allow-list Input Validation. 
    * Escaping all user input 

.NET Core is moderately defended against an SQL injection attack because the framework .NET core 5 has some defense against SQL-injections. We cannot use the input fields to directly communicate with the database. It would be an architectural challenge to introduce a vulnerability into our webapp.


# Break it

After our evaluation, we introduced vulnerabilities to our project. With a few tools, we were able to verify that it finds the vulnerabilities that we have introduced.


## Tools used

### Security Code Scan

Security Code Scan is an open source tool. It detects various security vulnerability patterns. We got the tool by downloading it via the extension manager in Visual Studio. The main reason for the usage of this tool is because it was recently updated, it's an active directory and works good.

More info about the tool on [the site](https://security-code-scan.github.io/#Installation)


### OWASP ZAP

OWASP ZAP is a tool outside of Visual Studio, it was an easy to install and setup with their [awesome guide](https://www.zaproxy.org/getting-started/). The tool itself is somehow 'scary', when u put the link of our site, the tool is going to try and break the site. The user interface is easy to understand even with no IT-knowledge.


### NoScript Extension

Noscript is an extension for chrome and Mozilla-based browsers, whenever a website tries to inject HTML or JavaScript code inside a different site, NoScript filters the malicious request and neutralizing its dangerous payload. 


### JSInjector Extension

Inject javascript is a chrome extension to injecti Java Script into a target URL every time that page loads.

You can inject your favourite script to extend the page functionality.

**Features:**
   * Add urls with or without regular expressions
   * Keep a list of your urls and edit your js code inline
   * You can turn on/off the js code when you wish
   * Search trough your URLs


## CSRF Vulnerability

We can create a simple CSRF vulnerability by removing all the code that creates a forgery token and the code that validates a forgery token. Not using a token for your cookies makes you vulnerable against CSRF. The authentication on our website would store a cookie which the attacker can now use to his advantage. Of course, the user would still have to be lured to the malicious site first. 

We took the ```[ValidateAntiForgeryToken]``` out to make the webapp vulnerable to CSRF.

To verify that our project is vulerable to CSRF we used our tool. Once we published the project, the tool immediately warns about potential vulnerabilities, as seen in the picture below.

![alt text](https://github.com/yfish1/SST23/blob/main/CSRFValidate.PNG)

However if you replace  ```[ValidateAntiForgeryToken]``` with ```[IgnoreAntiforgeryToken]```  the tool returns no warning.

We used a second tool (OWASP ZAP) to check XSS  and with the check it also gave us a warning for CSRF. 

## HTTPS Vulnerability

We tried to disable HTTPS and make our website not use an SSL however this is something that is configured centrally within azure. At most you can disable redirection to HTTPS and lower your TLS version but further than that Azure forces you to at least use HTTPS. In other cloud services this is also the case, AWS does also not compromise on this. 


## XSS

We have tried a few things for this subject matter:  

The first thing we’ve tried to do was entering [script/html tags](https://github.com/yfish1/SST23/blob/main/FrontPage1.PNG) in the input fields to see if the database would carry out the commands. We had no success trying this, the code was saved in the database as a string and was not executed. We tried to delete the regex that was provided by the tutorial but also this didn’t help.

```c#
       public class Movie
       {
        public int Id { get; set; }

        //[StringLength(60, MinimumLength = 3)]
        [Required]
        public string Title { get; set; }

        [Display(Name = "Release Date")]
        [DataType(DataType.Date)]
        public DateTime ReleaseDate { get; set; }

        //[Range(1, 100)]
        //[DataType(DataType.Currency)]
        //[Column(TypeName = "decimal(18, 2)")]
        public decimal Price { get; set; }

        //[RegularExpression(@"^[A-Z]+[a-zA-Z ]*$")]
        //@"^[a-zA-Z ]*$" 
        [Required]
        //[StringLength(30)]
        public string Genre { get; set; }

        //[RegularExpression(@"^[A-Z]+[a-zA-Z0-9""'\s-]*$")]
        //[StringLength(5)]
        [Required]
        public string Rating { get; set; }
        }
  ```

We also used a few tools to test on any XSS vulnerabilities. One of those tools was OWASP ZAP. This tool tried to break our site. It eventually ended up deleting all the data. The second tool we used is ‘Inject javascript’ extension. This extension allows us to inject scripts into the target URL (our webapp) but also this time nothing happened. We concluded from the several test we ran, that our webapp is XSS-proof. Unfortunately we could not introduce any vulnerabilities regarding this subject.   


## SQLi

The web application was built with a good framework so we didn’t think that we could introduce a SQL vulnerability. After manually testing and editing our source code for a few hours we accepted that it was not something that is open to introducing a vulnerability. Afterwards we tested our website for SQL injections on a third party tool. We were unsuccessful in introducing a vulnerability. 


# Journey

We first started with following a tutorial to setup the project. The setup went well and started up (locally) without problems. However, there were still some bugs, for example you couldn’t give a decimal number in the price column, we fixed this bug by alternating the regex. After fixing some minor bugs, we tried to host our project. This part took a lot of time of our hand. We tried to use AWS since this method was introduced in one of our classes, but we quickly realized that it was way too complicated, so we went further with Netlify but this was a fail too. After those 2 methods we chose for azure, a quite simple method, we thought. First we had to setup the azure account, to do this we had to verify our account as student. When we succeeded, we were given a student license. After this we made a resource group and published our project. Unfortunately it failed, we got an error.  

![alt_text](https://github.com/yfish1/SST23/blob/main/errorDevelopment.png)

We fixed this error by adding the “ASPNETCORE_ENVIRONMENT” and set the value to it to “Development”. Yet we still got errors after it, the second error was that the connectionString was wrong and we didn’t migrate it to the database. After we managed to clear the error by adding data manually to the local storage, we stumbled upon another error. This one was about the database, we didn’t connect with the azure database although we made it. After connecting the azure database everything worked. So we started to test if and how the project protects against the attacks named above. CSRF went fluently together with HTTPS, however we struggled with XSS and SQLi.  


# Feedback

Overall, we find this project a very good way of learning. We think our junior students should also get a similar project next year. There are many upsides but also a few downsides to this project. I will discuss these now. 

The biggest upside of this project is that we had the chance to explore security options with a very hands-on approach. Because of this we learned a lot of things about our curriculum and very fast. It is also important to note that we liked this way of learning which made everything a little easier. There is a downside to this, this way of learning is very time-consuming. In a matter of 6 weeks we have daily classes, weekly assignments, two projects and to top it off we have an exam at the end of this period. The timing of this project was a little awkward. 

Another upside is that we were quite free to do as we like. We assume that it is one of the things that we, IT-students, really enjoy. We really appreciate the freedom that we had within this project.  

We also have a downside to not for this project, we had to build our own webstore. In this case it is very hard to create a website in a language that you do not know and then must find vulnerabilities in this language. It made this project much more complex than it had to be. A suggestion we had was that we would get a website and we would have to analyze (more) vulnerabilities on that website. Lastly, we had different assignments for every group. This is not necessarily bad, but the assignments all covered different subjects. This means we are being graded for different parts and goes against the principle of equal education for all students.  

We hope that this feedback can be taken constructively and offered some additional insights for you. We would like to thank you for your classes, without a brown-nose we can all say that we enjoyed your curriculum. 
