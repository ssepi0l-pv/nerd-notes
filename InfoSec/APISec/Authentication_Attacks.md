# Classic authentication attacks.

For demonstration purposes I'll be using crAPI hosted on crapi.apisec.ai. I'll route all trafic using FoxyProxy to Burpsuite.

## Logging logins and signins.

Using Burpsuite, we can intercept requests using the Proxy module. We can sign in, take the request from Burpsuite and save it
for later in the Organizer, maybe add a note explaining the purpose of the request itself.

We should also repeat the request using the Repeater module, to check how the webpage responds. In the case of crAPI, it returns
a Bearer token for authentication. 

Something I found interesting is that when I repeat a request without a password, the login API drops a validation error:
~~~
"details":"org.springframework.validation.BeanPropertyBindingResult: 2 errors\nField error in object 'loginForm' on field 
'password': rejected value []; codes [NotBlank.loginForm.password,NotBlank.password,NotBlank.java.lang.String,NotBlank]; 
arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [loginForm.password,password]; arguments []; 
default message [password]]; default message [no debe estar vacío]\nField error in object 'loginForm' on field 'password': 
rejected value []; codes [Size.loginForm.password,Size.password,Size.java.lang.String,Size]; 
arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [loginForm.password,password]; 
arguments []; default message [password],40,4]; default message [size must be between 4 and 40]"
~~~

I'm not really sure if this is intended to be the default (insecure) configuration; I think this might fall under the Sensitive
Data Exposure, since it kinda does give a small insight into the code.


