# Two Factor Authentication with Django and Nexmo Verify

I showed my love for two factor authentication last month with a demo application for my "Kittens & Co" business. Interestingly enough not everyone is equally a fan of cats, some of us prefer dogs, some of us prefer other animals, but we all love two factor authentication, right?

## Let's have a little poll

For this tutorial I am going to show you how to add two factor authentication to your Django site using the [Nexmo Verify API](https://www.nexmo.com/products/verify/). For this purpose I have built a little app called [“Pollstr”](https://github.com/nexmo-community/nexmo-django-2fa-demo) – a simple web app for doing polls. I know it's going to be an overnight success because of the missing "e" in the name.

![Pollstr](images/screen1.png)

You can download the starting point of the app from Github and run it locally.

```sh
# ensure you have Python and pip installed
git clone https://github.com/nexmo-community/nexmo-django-2fa-demo.git
cd nexmo-django-2fa-demo
pip install -r requirements.txt
python manage.py migrate
python manage.py loaddata fixtures/all.json
python manage.py runserver
```

Then visit [127.0.0.1:8000](http://127.0.0.1:8000) in your browser and try to vote on a poll. You can log in with the username `test` and password `test1234`.

By default the app implements registration and login using built in auth framework but most of this tutorial applies similarly to apps that use other authentication methods. Additionally we added some bootstrap for some prettyfication of our app.

All the code for this starting point can be found on the [before](https://github.com/nexmo-community/nexmo-django-2fa-demo/tree/before) branch on Github. All the code we will be adding below can be found on the [after](https://github.com/nexmo-community/nexmo-django-2fa-demo/tree/after) branch. For your convenience you can see [all the changes between our start and end point](https://github.com/nexmo-community/nexmo-django-2fa-demo/compare/before...after) on Github as well.

## Nexmo Verify for 2FA

[Nexmo Verify](https://www.nexmo.com/products/verify/) is the easiest way to implement phone verification. In most two factor authentication systems you will need to manage your own tokens, token expiry, retries, and SMS sending. Nexmo Verify manages all of this for you and all it requires is just 2 API calls!

To add Nexmo Verify to our app we are going to make the following changes:

* Add a `phone_number` to our user
* Add a `TwoFactorMixin` to our views to ensure the user is logged in and verified
* Send the user a verification code
* Verify the code sent to their number

## Adding a phone number
