---
title: "Simple but effective way of handling state and business logic in Flutter"
date: 2019-07-19
draft: false
toc: false
images:
tags:
  - programming
  - flutter
---

When it comes to learning [Flutter](https://flutter.dev), my observation is that a lot of people get stuck when they attempt to do anything outside of the tutorials and guides.

This is true for all things in software development, but I think Flutter has it worse because it happens regardless of how much experience the developer has under their belt. My guess is that it happens for the following reasons:

- Flutter is a framework that provides a lot of freedom to developers.
- There are too many choices and opinions when it comes to how things are done.
- The Flutter team is not opinionated as to how things should be done.

These things can be good or bad depending on the developer. I used Ruby on Rails for the majority of my career and I was able to witness its influence to software development so I am convinced that the things I listed above are leaning more towards the downside.

I think Flutter should be opinionated and there should be an official way of doing things. Sure, you'd be giving up the freedom of being able to do things your way, but in exchange, you'll be a lot more productive because it will be easier to learn and be on the same page as everyone else. Being opinionated also makes it easier to transition between projects because they'll all be structured the same way.

Enough with my opinion though. You didn't read this article to hear about what I think Flutter should be. Let's just get to the good stuff.

In this post, I am going to share the things I've learned when it comes to architecting your Flutter app.

## Managing State

At some point in your journey to learning Flutter, I'm sure you've always wondered how you're going to manage the state in your app. If you're a beginner to frontend development, you might not really know why you should even bother with proper state management in the first place. However, everyone talks about state management so, of course, we are going to find the best way to do it.

If you're experienced with frontend development, it's such a hassle to explore all of the ways to manage state, having to pick one, and stick with it. Why couldn't the Flutter team just tell us what to use?

Fortunately, it finally happened. In the recent Google I/O 2019, the Flutter team finally endorsed an "official" way of handling state.

{{< youtube d_m5csmrf7I >}}

I added the quotes because at the end of the video, they mentioned that there is no silver bullet when it comes to state managment. You can still use whatever method you want to manage state.

Back in Google I/O 2018, they [endorsed using Bloc](https://www.youtube.com/watch?v=RS36gBEp8OI) to manage state, but I think that method is convoluted especially for beginners or small-scale apps. The method they mentioned in the 2019 video is way better and that's what we're going to use in this post. However, I'm going to add my own twist to it.

Let's build an app that lets us sign up for an account! We're going to start with creating a class for storing the state.

```dart
// lib/state/authentication_state.dart

import 'package:flutter/foundation.dart';

class AuthenticationState extends ChangeNotifier {
  bool _authenticated = false;

  bool get authenticated => _authenticated;

  void authenticationSuccess() {
    _authenticated = true;
    notifyListeners();
  }
}
```

Similar to the [endorsed way of managing state](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple), we are going to create a class that extends `ChangeNotifier`. However, instead of using it directly inside a model, we are using `ChangeNotifier` on a class that's created specifically for storing state.

Now that we're tracking the status of the user's authentication, let's make some changes to `lib/main.dart`:

```dart
// lib/main.dart

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import 'package:my_app/state/authentication_state.dart';
import 'package:my_app/widgets/main_view.dart';

void main() {
  runApp(MyApp);
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      // ignore: always_specify_types
      providers: [
        ChangeNotifierProvider<AuthenticationState>(
          builder: (BuildContext context) => AuthenticationState(),
        ),
      ]
      child: MaterialApp(
        title: 'MyApp',
        home: MainView(),
      );
    );
  }
}
```

Again, similar to the [endorsed way of managing state](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple), we are using the [Provider package](https://pub.dev/packages/provider) to make our state available all throughout the app.

If you're not familiar with Provider, it's a package that provides (heh) a convenient way of using [InheritedWidget](https://api.flutter.dev/flutter/widgets/InheritedWidget-class.html), which is a class you can use for propagating information down the widget tree. Provider, by itself, doesn't let you do state management directly. It simply gives you a better way of interfacing your widgets with your chosen method of state management. This means you can use BLoC, redux, MobX or whatever, but we're choosing to use [ChangeNotifier](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html) because it's easy to learn and powerful enough to scale.

There are 3 things that might have caught your attention in the above example. The first thing is this:

```dart
return MultiProvider();
```

We can definitely use `ChangeNotifierProvider()` directly, but we'll be adding another provider later, so let's just use `MultiProvider` already to make things easier for us later on.

Next, we have this comment:

```dart
// ignore: always_specify_types
```

If you used the same [linting configuration as the Flutter repo](https://github.com/flutter/flutter/blob/master/analysis_options.yaml), Dart will ask for the [List type](https://api.dartlang.org/stable/2.4.0/dart-core/List-class.html) for the following code:

```dart
providers: [
  ChangeNotifierProvider(),
],
```

I honestly don't know what type to put here because we won't be using just `ChangeNotifier` later on. We added this comment so `flutter analyze` won't return an error on this line. That said, please let me know if you know the type to use here.

Next is this part:

```dart
child: MaterialApp(
  title: 'MyApp',
  home: MainView(),
);
```

We're using a class called `MainView` as our entry point to the application. If prefer having something like this to keep `main.dart` as short as possible. It also allows us to work on a completely separate file for doing app-level stuff. You really should only modify `main.dart` for the very important stuff like initializing state or other classes that are to be passed through Provider.

Next, let's check out what `MainView` is all about:

```dart
// lib/widgets/main_view.dart

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import 'package:my_app/screens/dashboard_screen.dart';
import 'package:my_app/screens/sign_in_screen.dart';
import 'package:my_app/state/authentication_state.dart';

class MainView extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Consumer<AuthenticationState>(
      child: SignInScreen(),
      builder: (BuildContext context, AuthenticationState authenticationState, Widget child) {
        if (authenticationState.authenticated) {
          return DashboardScreen();
        }

        return child;
      },
    );
  }
}
```

The basic gist of `MainView` is that if the user is authenticated, the app should redirect the user to a screen dedicated for authenticated users (`DashboardScreen`). If not, the app should take the user to a login screen instead (`SignUpScreen`).

We'll get to those later. In the mean time, I'll explain the stuff from Provider.

[`Consumer`](https://pub.dev/documentation/provider/latest/provider/Consumer-class.html) is one of two ways to retrieve an object you passed through Provider. You should use `Consumer` when you're trying to build a widget that's based on a value that changes a lot. In our case, that is `AuthenticationState`.

`Consumer` has an optional argument called `child` that lets you build a widget exactly once and inject that directly into the `builder` method. It's a good idea to utilize this option when you have a widget that doesn't depend on any values from Provider because the widget won't get rebuilt whenever something inside Provider changes. It's more efficient in terms of performance.

## Handling Business Logic

In this section, we're going to cover how to handle business logic. Before we get to that though, let's create the login screen I mentioned in the previous section:

```dart
import 'package:flutter/material.dart';

import 'package:my_app/services/authentication_service.dart';

class SignInScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Log In'),
      ),
      body: SignInForm(),
    );
  }
}

class SignInForm extends StatefulWidget {
  @override
  SignInFormState createState() {
    return SignInFormState();
  }
}

class SignInFormState extends State<SignInForm> {
  final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
  final TextEditingController _emailController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();
  final RegExp emailRegex = RegExp(r'^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}$');

  @override
  void dispose() {
    super.dispose();
    _emailController.dispose();
    _passwordController.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: <Widget>[
          _emailField(),
          const SizedBox(height: 20.0),
          _passwordField(),
          const SizedBox(height: 15.0),
          _signInButton(),
        ],
      ),
    );
  }

  Widget _emailField() {
    return TextFormField(
      controller: _emailController,
      decoration: InputDecoration(
        labelText: 'Email Address',
        hintText: 'Enter your email address',
      ),
      keyboardType: TextInputType.emailAddress,
      validator: (String value) {
        if (value.isEmpty) {
          return 'Please enter your email address';
        } else if (!emailRegex.hasMatch(value)) {
          return 'Please enter a valid email address';
        }
      },
    );
  }

  Widget _passwordField() {
    return TextFormField(
      controller: _passwordController,
      decoration: InputDecoration(
        labelText: 'Password',
        hintText: 'Enter your password',
      ),
      keyboardType: TextInputType.text,
      obscureText: true,
      validator: (String value) {
        if (value.isEmpty) {
          return 'Please enter your password';
        }
      },
    );
  }

  Widget _signInButton() {
    final AuthenticationService authenticationService =
        AuthenticationService(context: context, formKey: _formKey);

    return RaisedButton(
      child: const Text('Log In'),
      onPressed: authenticationService.signIn(_emailController, _passwordController);
    );
  }
}
```

This screen was taken straight out of [Flutter's guide on how to build forms](https://flutter.dev/docs/cookbook/forms/retrieve-input). I added a few things and the most important part which is the function that the Login button will execute once it's pressed.

```dart
Widget _signInButton() {
  final AuthenticationService authenticationService =
      AuthenticationService(context: context, formKey: _formKey);

  return RaisedButton(
    child: const Text('Log In'),
    onPressed: authenticationService.signIn(_emailController, _passwordController);
  );
}
```

We're creating an instance of a class called `AuthenticationService` and calling its `signIn` method. The rationale here is that widgets should only be able to have read-access when it comes to data by accessing the state or any data using Provider. If it wishes to do any mutation, it should call a service object and let it handle things from there.

Let's look at `AuthenticationService`:

```dart
// lib/services/authentication_service.dart

import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

import 'package:rarex/state/authentication_state.dart';

class AuthenticationService {
  AuthenticationService({
    @required this.context,
    this.formKey,
  }) {
    authenticationState = Provider.of<AuthenticationState>(context);
  }

  BuildContext context;
  GlobalKey<FormState> formKey;
  AuthenticationState authenticationState;

  Future<void> signIn(TextEditingController emailController, TextEditingController passwordController) async {
    if (formKey.currentState.validate()) {
      authenticationState.authenticationSuccess();
      Navigator.of(context).pop();
    }
  }
}
```

Let's go over this class and its contents:

```dart
AuthenticationService({
  @required this.context,
  this.formKey,
}) {
  authenticationState = Provider.of<AuthenticationState>(context);
}

BuildContext context;
GlobalKey<FormState> formKey;
AuthenticationState authenticationState;
```

`AuthenticationService` accepts a `BuildContext` and an optional `GlobalKey<FormState>`. The context is required so we can call whatever data we need from Provider as well as have access to the navigator. The form key is required to do the validation inside of the service class instead of inside the button's `onPressed` attributes.

Once initialized, `AuthenticationService` will call Provider so it can have access to `AuthenticationState`.

```dart
Future<void> signIn(TextEditingController emailController, TextEditingController passwordController) async {
  if (formKey.currentState.validate()) {
    authenticationState.authenticationSuccess();
    Navigator.of(context).pop();
  }
}
```

This is the good stuff. `signIn()` takes 2 text controllers (for the email and password fields) which we will be using later on in this post. At the moment, it will only see if the form fields are valid. In case it's not clear, these are the validations that will execute once `formKey.currentState.validate()` is called:

```dart
// inside _emailField in SignInScreen
validator: (String value) {
  if (value.isEmpty) {
    return 'Please enter your email address';
  } else if (!emailRegex.hasMatch(value)) {
    return 'Please enter a valid email address';
  }
},

// inside _passwordField in SignInScreen
validator: (String value) {
  if (value.isEmpty) {
    return 'Please enter your password';
  }
},
```

`formKey.currentState.validate()` returns true when the fields inside the form associated with the given form key have valid inputs. Otherwise, it returns false and changes the affected form fields into an error state (the fields turn red and displays the error message).

Once the form has passed the basic validation, `AuthenticationService` will call a state mutation called `authenticationSuccess()`. As you remember from earlier, `authenticationSuccess()` changes the value of `authenticated` from false to true. Here it is in case you forgot and are too lazy to scroll up:

```dart
void authenticationSuccess() {
  _authenticated = true;
  notifyListeners();
}
```

Also, `signIn()` calls `Navigator.of(context).pop()`. Since `authenticated` is now true at this point, `MainView` will render `DashboardScreen` now instead of `SignInScreen`. Here is the code again in case you forgot what it looked like:

```dart
// inside MainView
Widget build(BuildContext context) {
  return Consumer<AuthenticationState>(
    child: SignInScreen(),
    builder: (BuildContext context, AuthenticationState authenticationState, Widget child) {
      if (authenticationState.authenticated) {
        return DashboardScreen();
      }

      return child;
    },
  );
}
```

## Conclusion

In conclusion, let's summarize the things we talked about in this post:

- State is stored inside the state classes (`lib/state/authentication_state.dart`). The state class also provides methods for mutating the state.
- Business logic is handled by service classes (`lib/services/authentication_service.dart`).
- The state can be accessed by using the Provider package (`Provider.of<AuthenticationState>(context)` or `Consumer<AuthenticationState>()`)
- Service classes have access changing the state and other business logic.
- Widgets should only have read access to the state. Mutation should only be done by service classes.
- If a widget wishes to do any mutation (state or business logic), they should call a service class method (`AuthenticationService.signIn`)

Thanks for reading!
