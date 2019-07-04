---
title: "How to allow approved mobile applications to authenticate seamlessly inside OAuth 2.0"
date: 2014-12-17
draft: false
toc: false
images:
tags:
  - programming
  - ruby-on-rails
---

I have an existing API server built with Rails, Devise, and the Doorkeeper gem. There’s also a mobile app that wants to use the API for authentication. By default, what will happen is that the client (or mobile app in this case) is going to be redirected to the server’s sign in page and then back to the client.

This kind of flow is fine if you’re in a web browser. It’s different when it comes to mobile apps though because the app will open a web view just to authenticate. This doesn’t look good from a UX perspective because it feels like the user is leaving the app.

The ideal situation would be to just have the login fields in the very front of the app and take them straight to the home view afterwards. Luckily, there’s a way to authenticate users directly without having to open the sign-in page from the API server; It’s called Resource Owner Password Credentials flow.

Resource Owner Password Credentials flow is an OAuth 2.0 authentication approach where the client can just send the username and the password directly to get an access token from the server. This is normally done if the client is a trusted source, which, in this case, is an internal mobile app. This is what Facebook and Twitter has done with their mobile apps.

To support ROPC (yes, it’s too long to type every time), you need to set up Doorkeeper for it:

```ruby
# config/initializers/doorkeeper.rb

Doorkeeper.configure do
  resource_owner_from_credentials do |routes|
    u = User.find_for_database_authentication(:email => params[:username])
    u if u && u.valid_password?(params[:password])
  end
end
```

Once you’ve done that, restart the app and it will just work. Your app will now support ROPC. Here’s a sample cURL script that you can do to make an authorization request:

```shell
curl -H “Content-Type: application/json" \
-d '{"client_id":"a11bbd9c339e6d8bd84368061089971398e23617d88a7c8dd060555b7cff1077","client_secret":"f079dadcca08a26d5967421f3849447e1e4519d6ba33cabdd4bdd0c86aebded3","grant_type":"password","username":"test@example.com","password":"passw0rd"}' /
-X POST http://localhost:3000/oauth/token
```

Take note that we’re setting “grant_type” to “password” to let the provider know that we’re going to authenticate using ROPC. The provider will return this as a response once you’ve successfully authenticated:

```shell
{“access_token":"aa7511182a980a31f0f1bacfa396a7d7c1bb082fcd6c5e24970e9a15473f0eb9","token_type":"bearer","expires_in":7200,"refresh_token":"b80adab50c6e851525152e24a4b48721d47dcf21cf0701263564b3ccffeb3619"}
```

You can use the access_token in your requests to get past endpoints that are protected by OAuth 2.0

Let’s pretend that we have a /users endpoint that returns a list of users when it gets a successful request. Here’s what the cURL script would look like:

```shell
curl -H "Content-Type: application/json" \
-d '{"access_token":"aa7511182a980a31f0f1bacfa396a7d7c1bb082fcd6c5e24970e9a15473f0eb9"}' \
--include -X GET https://localhost:3000/api/v1/users
```

That’s it. I’m not a mobile developer, so converting these cURL scripts to your respective platform is entirely up to you.