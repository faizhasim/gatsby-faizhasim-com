# Yet another complain on plaintext password

:published_at: 2014-08-14
:hp-tags: 


##### This time, I sent a formal email to Pizza Hut delivery. Below is my email to them:


Hi

I’m a customer using your online service to order pizzas and I would like to lodge a complain on how poor you handle customer password. According to your policy at http://www.phdelivery.com.my/privacy_pdppolicy.php, you mentioned that "**The Company shall at its best keep and process your data in a secure manner**”. But, I want to raise  2 major security issue with on how you handle our password.



## Issue 1: Password stored and exposed to customer as plain text

image::https://cloud.githubusercontent.com/assets/898384/10412836/b68f6ec6-6fc4-11e5-9ae6-e6d0a056b580.png[]

I have consulted Malaysian Communications and Multimedia Commission (MCMC) on the how a company should store user password and this is not acceptable.

Password **should be minimally encrypted by one-way cryptographic hashing mechanism**, for example at least using SHA-128 algorithm for a non-financial service. This means, that even Pizza Hut should not know customer password, but password verification is done by comparing the hashed password.

**OhMedia!** social service has been hacked due to plaintext password storage reported at http://dazzlepod.com/ohmedia/. Data from http://thepasswordproject.com/leaked_password_lists_and_dictionaries proof that major contribution to cracked passwords are due to plaintext password.





## Issue 2: Unable to change password

I am trying to change my password to a more secure, randomly generated password. But, phdelivery.com.my keep insisting me to use my old password. This way of handling user passwords is completely broken and does not make any sense.



Looking for a good reply from Pizza Hut.






A concern customer,

Mohd Faiz bin Hasim



