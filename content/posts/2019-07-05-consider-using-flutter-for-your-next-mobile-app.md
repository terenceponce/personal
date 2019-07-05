---
title: "Consider using Flutter for your next mobile app"
date: 2019-07-05
draft: false
toc: false
images:
tags:
  - programming
  - flutter
---

In this blog post, I am going to try and convince you to use Flutter instead of React Native for your next mobile app.

Ever since [Flutter hit version 1.0](https://developers.googleblog.com/2018/12/flutter-10-googles-portable-ui-toolkit.html), I have been giving a lot of attention to Flutter. It has been a challenge, but it has been worth the effort so far since I was able to convince my company to make the switch from React Native. The development process has become a lot more sane since we've made the switch.

I'm sure you already know what Flutter is, but on the off-chance that you don't, [Flutter](https://flutter.dev) is a cross-platform mobile development framework made by Google. This is Google's answer to Facebook's [React Native](https://facebook.github.io/react-native/) and Microsoft's [Xamarin](https://visualstudio.microsoft.com/xamarin/).

The rest of this post will revolve around Flutter and React Native because RN is the most popular cross-platform framework right now which makes it a good point for comparison. Also, I don't have a lick of experience in Xamarin, so there's no point in me talking about that in this post.

## Why Flutter?

In recent times, when it comes to developing mobile apps, you have 2 ways of going about it:

1. Use iOS and Android's respective tools for creating native mobile apps.
2. Use a cross-platform framework for creating native mobile apps.
3. ~~Use an old school cross-platform tool like Phonegap to create hybrid apps~~ Just kidding.

Most developers/companies would go for option 2 because it's simply the better option for small teams. Cross-platform frameworks have come such a long way that when it comes to speed, you can't even tell which ones are created using cross-platform frameworks to those that are not. This makes it all the more appealing.

Now, when it comes to option 2, the most popular solution would be to use React Native. Flutter has been gaining traction, but I'm sure it will take a while before it becomes the de-facto option.

If you're someone who has a decent amount of experience with React Native, I'm sure you have your grievances about it as well as I did.

I'm going to be listing some things that were frustrating in RN and comparing them to Flutter.

## Installation Process / Tooling

### React Native

When trying out React Native, the first challenge you'll be facing is installing the thing. Their [Getting Started docs](https://facebook.github.io/react-native/docs/getting-started) encourage using Expo instead of the vanilla way because it's a whole lot easier to get an app running that way.

Expo is a toolkit that provides a lot of convenience for React Native developers. Here are the features that I can remember off the top of my head:

- Shaves the amount of friction when it comes to starting a React Native project.
- Has a build service that does the building and signing of your app.
- Provides a lot of APIs for accessing a phone's features without having to rely on a 3rd party package.
- Allows the developer to use React Native without having to deal with Android Studio or Xcode.

While it is very convenient to use Expo, it has some disadvantages that may be a dealbreaker for some people:

- The generated builds are larger in size compared to builds generated from the vanilla approach.
- Your codebase has to be completely Javascript only which limits the amount of packages that you can use.
- Running your app on a local device requires you to install Expo's mobile app and having to do a few additional steps if you're testing on iOS.

If you're like me, you're going to want to go the vanilla way instead of Expo because you want to adhere to the official way of doing things. It also eliminates the disadvantages you would be getting from Expo.

Unfortunately, doing a vanilla installation of React Native can be a bit of a challenge. Unless you know what you're doing, most people would fail the installation process. This is why Expo is so popular among React Native developers.

### Flutter

When it comes to [setting up Flutter](https://flutter.dev/docs/get-started/install), I like to describe it to other people as combining the easy installation process of Expo while having the benefits of the vanilla way of installing React Native.

The Flutter tool provides a command called `doctor` that lets you know what you're missing from your setup. Here's an example:

```shell
$ flutter doctor
[-] Android toolchain - develop for Android devices
    • Android SDK at /Users/obiwan/Library/Android/sdk
    ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
    • Try re-installing or updating your Android SDK,
      visit https://flutter.dev/setup/#android-setup for detailed instructions.
```

In the above snippet, Flutter tells us that while Android SDK has been installed, it's missing some components that prevents us from completing the installation.

React Native does not have something like this. At best, it will give you a stack trace and hope to God that pasting it to Google will lead to a solution.

Here's an example of a successful Flutter installation:

```shell
$ flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.5.4-hotfix.2, on Mac OS X 10.14.5 18F132, locale en-PH)

[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[✓] iOS toolchain - develop for iOS devices (Xcode 10.2.1)
[✓] Android Studio (version 3.1)
[✓] VS Code (version 1.36.0)
[✓] Connected device (1 available)

• No issues found!
```

This isn't really that big of a deal since you only really encounter the installation friction in React Native once, but seeing how Flutter does their tooling/installation sets a really good first impression of how good Flutter can be.

## Language Choice

React Native uses Javascript out of the box while Flutter uses Dart. Almost everyone and their mothers know what Javascript is so I'm going to go over Dart.

Dart is an object-oriented language that was made by Google specifically for creating client applications. It was also designed to be multiplatform, so theoretically, you can use it everywhere.

It uses a C-style syntax so it's very easy to pick up. I, myself, just blazed through [Dart's language tour](https://dart.dev/guides/language/language-tour) and their [Migrating from React Native docs](https://flutter.dev/docs/get-started/flutter-for/react-native-devs) and that was enough for me to write code in Flutter.

It's also statically typed and as someone who's been using Ruby and other dynamic languages for the longest time, I am very welcoming of this feature. There's an option to use Flow or TypeScript in React Native, but it's not exactly a cake walk. There's a lot of friction when it comes to using those that I would often revert back to using just plain Javascript.

One thing that I would have liked having in Dart is an interactive shell to play with. It's one thing that I miss from Ruby and Elixir because it lets you play around and experiment which is almost a must-have for learning a new language.

Let's go over how a React Native component looks if it was written as a Flutter widget.

Here is a Sign In form that's written in React Native:

```javascript
import React from "react";
import { Button, TextInput, View } from "react-native";

class SignInScreen extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      email: "",
      password: "",
    };

    this.handleSubmit = this.handleSubmit.bind(this);
  }

  render() {
    const { email, password } = this.state;

    return (
      <View>
        <Text>Email</Text>
        <TextInput
          placeholder="Email Address"
          onChangeText={value => this.setState({ email: value })}
          value={email}
        />
        <Text>Password</Text>
        <TextInput
          placeholder="Password"
          onChangeText={value => this.setState({ password: value })}
          value={password}
          secureTextEntry
        />
        <Button onPress={() => {
          const { email, password } = this.state;

          // TODO: Pass the values to some handler
          // signIn(email, password)
        }}>
          Login
        </Button>
      </View>
    );
  }
}

export default SignInScreen;
```

Here is what the above example would look like in Flutter:

```dart
import 'package:flutter/material.dart';

class SignInForm extends StatefulWidget {
  @override
  SignInFormState createState() {
    return SignInFormState();
  }
}

class SignInFormState extends State<SignInForm> {
  final TextEditingController _emailController = TextEditingController();
  final TextEditingController _passwordController = TextEditingController();

  @override
  void dispose() {
    super.dispose();
    _emailController.dispose();
    _passwordController.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        _emailField(),
        _passwordField(),
        _signInButton(),
      ],
    );
  }

  Widget _emailField() {
    return TextField(
      controller: _emailController,
      decoration: InputDecoration(
        labelText: 'Email Address',
      ),
    );
  }

  Widget _passwordField() {
    return TextField(
      controller: _passwordController,
      decoration: InputDecoration(
        labelText: 'Password',
      ),
      obscureText: true,
    );
  }

  Widget _signInButton() {
    return RaisedButton(
      child: const Text('Login'),
      onPressed: () {
        // TODO: Pass the values to some handler
        // signIn(emailController.text, passwordController.text)
      }
    );
  }
}
```

The Flutter version is not an exact 1:1 copy, but it is almost there.

If I'm being honest, React Native looks better although Dart looks good in its own way as well. As soon as you get used to Dart (and you will), you won't even notice the difference anymore since it's going to be the same in your brain. React Native wins this round though.

Next, let's look at what asynchronous code looks like on both. I'm going to take the next example straight out of [the Migrating From React Native guide](https://flutter.dev/docs/get-started/flutter-for/react-native-devs):

```javascript
// JavaScript
class Example {
  _getIPAddress() {
    const url = 'https://httpbin.org/ip';
    return fetch(url)
      .then(response => response.json())
      .then(responseJson => {
        const ip = responseJson.origin;
        return ip;
      });
  }
}

function main() {
  const example = new Example();
  example
    ._getIPAddress()
    .then(ip => console.log(ip))
    .catch(error => console.error(error));
}

main();
```

```dart
// Dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class Example {
  Future<String> _getIPAddress() {
    final String url = 'https://httpbin.org/ip';
    return http.get(url).then((response) {
      String ip = jsonDecode(response.body)['origin'];
      return ip;
    });
  }
}

main() {
  final Example example = new Example();
  example
      ._getIPAddress()
      .then((ip) => print(ip))
      .catchError((error) => print(error));
}
```

Both examples are pretty similar. Javascript uses `Promise` while Dart uses `Future`. Dart gains an edge though when it comes to `async` and `await`:

```javascript
// JavaScript
class Example {
  async _getIPAddress() {
    const url = 'https://httpbin.org/ip';
    const response = await fetch(url);
    const responseJson = await response.json();
    const ip = responseJson.origin;
    return ip;
  }
}

async function main() {
  const example = new Example();
  try {
    const ip = await example._getIPAddress();
    console.log(ip);
  } catch (error) {
    console.error(error);
  }
}

main();
```

```dart
// Dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class Example {
  Future<String> _getIPAddress() async {
    final String url = 'https://httpbin.org/ip';
    final http.Response response = await http.get(url);
    String ip = jsonDecode(response.body)['origin'];
    return ip;
  }
}

main() async {
  final Example example = new Example();
  try {
    final String ip = await example._getIPAddress();
    print(ip);
  } catch (error) {
    print(error);
  }
}
```

Dart's `Future` lets you set the expected return object which makes the output predictable when using `await`. Dart also lets you know if the `Future` returns something unexpected at compile time. In Javascript land, you will probably encounter the bug at run time and have to deal with it from there.

The takeaway I'd like you to have from this section is that it's easy to pick up Flutter if you already know React Native. You can spend a few weeks on a pet project and I can almost guarantee that you'll be able to get to the same level in Flutter as you were in React Native.

Once you get the hang of Flutter, it should be obvious to you that Google knew what they were doing when they made Flutter. It was built from the ground up to be used for cross-platform mobile development. It's great that Facebook is [dogfooding](https://en.wikipedia.org/wiki/Eating_your_own_dog_food) React Native. However, it doesn't feel as polished when it comes to tooling compared to Flutter.

## The Future of Flutter and Dart

To segue to a related topic (future, heh), I'd like to point out that Google owns both Dart and Flutter. This means that Dart is changing to make Flutter code look better and may eventually surpass Javascript inside React Native. It's not too farfetched because [that is what has been happening recently](https://medium.com/dartlang/announcing-dart-2-3-optimized-for-building-user-interfaces-e84919ca1dff).

Flutter is being expanded to [web](https://flutter.dev/web) and [desktop](https://github.com/google/flutter-desktop-embedding). As it looks right now, the possibility of having a single shared codebase for web, desktop, and mobile is pretty good. Here's a good example of a functioning Dart codebase that runs on Flutter and Angular (through [AngularDart](https://angulardart.dev/)): https://github.com/roughike/inKino

There are exciting times ahead of Flutter and it's looking to be something that will greatly reward developers and companies for investing into.

## To Be Continued

I'd like to cover a few more points about this subject, but this post is getting very long to read. I will be making another post in the future about this topic. Thanks for reading!
