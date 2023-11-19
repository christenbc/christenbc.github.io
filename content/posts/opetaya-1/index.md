---
title: "Opetaya Series: A journey from zero to a functional social network (Part 1)"
date: 2021-12-15T00:28:54+01:00
categories:
- entrepreneurship
- bootstrapping
tags:
- entrepreneurship
- website
- saas
- baas
- firebase
- flutter
- dart
- nodejs
- ios
- android
- applestore
- googleplay
- swift
- java
- kotlin
- startup
- cloud
- server
- digital
- bootstrapping
- bloc
- market
- users
- opetaya
- mobirise
- mvp
- github
keywords:
- entrepreneurship
- website
- saas
- baas
- firebase
- flutter
- dart
- nodejs
- ios
- android
- applestore
- googleplay
- swift
- java
- kotlin
- startup
- cloud
- server
- digital
- bootstrapping
- bloc
- market
- users
- opetaya
- mobirise
- mvp
- github
comments: true
---

From nothing to a fully operative social network available for Android and iOS with a landing page. How I managed to create all of that on my own by investing lots of time and less than €200. Follow my journey.
<!--more-->

In this post, you will learn my thought process to create a digital production-ready product.

<p></p>
{{< figure align="center" src="images/use-case.jpg" title="Opetaya use case" >}}

This is the beginning of a short series in which I will document juicy know-how.  
So, stay tuned!

---

# Introduction

Before starting the project that led to the creation of [Opetaya](https://opetaya.com/), I heard many times you need a lot of money or get a fundraised to kick-start a startup. I proved myself this is a false statement with the correct strategy and under certain factors.

It is not the same to create a company based on the production of orange juice than creating a company like _Twitter_. The reason relies on the required resources to create an <abbr title="Minimum Viable Product">MVP</abbr>. Currently, it is immensely cheaper* to kick-start a digital business than a traditional business which requires significant material assets or logistics to operate. In addition, a digital company can by-pass, in higher degree, the need of owning real-estate for operating, as most of the activities can be performed remotely.

What is the main disadvantage for entrepreneurs aiming to create digital products?

Clearly, the lack of technical knowledge in software. This is key in order to succeed. But, there is always an alternative as long as you have money. There are people and companies eager to provide their services to entrepreneurs that lack of time or software knowledge required to build a digital MVP.

Naturally, the outsourcing approach implies additional costs. Which depending on the type of MVP can range from a couple of thousands of euros to dozens of thousands easily, if not more. Have in mind that software development is a complex activity which takes a considerable amount of time and effort, especially because there are usually setbacks induced by bugs [^1] in the code.

In my case, neither I could afford to outsource the software development nor wait for someone to invest in my project. My only assets were a laptop and a spare screen. So I opted for getting my hands dirty and doing everything on my own, this is also known as _bootstrapping_.

{{< figure align="center" src="images/bootstrapping.jpg" >}}

The idea was to create a social network for a niche of market for mobile devices. But, the problem was I had no idea about mobile software development. I just was familiar with _C++_, _Python_, _Matlab_ and a little of _version control_. Then I thought, what if I just start to research on the Internet about how to code for mobile devices?

> A simple yet powerful action of googling "how to create an app for android and iOS" originated a chain effect that led to everything else.  

I realized it is totally feasible to leverage on _Youtube_ content for serious projects. As opposed to the mainstream idea that Youtube is just for leisure, it is actually the modern _Library of Alexandria_. Top-notch free informative content that allows you to get started in almost any imaginable discipline.

At some point of the learning experience, I needed more specialized content. That is when alternative paid platforms like _Udemy, Coursera, Udacity_ play the role. Some of these resources offer mentorship via forums or communication platforms like _Discord_ in which it is possible to ask anything.

In my case, I chose _Udemy_. It is a video platform which offers a wide range of learning products in a democratic way. Almost anyone can offer their know-how as an _Udemy_ mentor and get paid for it. The reasons were mainly because of:
- it is fairly cheap when discount promotions take place (which is often)
- provides very specialized content

However, in reality, you cannot rely on just an online course for the completion of a production-ready project. The way is actually a long path of research on different resources, asking strangers on the Internet, known people and hard work.

# Software Development

Most of the added value of mobile apps consists on the fact that they are synced with the world. Take a look in your smartphone and verify that most of your apps require Internet connection in order to operate. There are exceptions like the alarm clock, the calculator and few more. But, certainly most of the time you use your smartphone, you are using the Internet through the browser or an app.

A mobile app takes time to load and upload stuff from the Internet and sometimes is out of connection because the smartphone in which is installed, is in the middle of a nowhere.

All this makes software development harder to conceive. However, the solution to this is _asynchronous programming_. 

{{< figure align="center" src="images/async.jpg" title="Synchronous vs Asynchronous programming" >}}

On the other side, what is the Internet exactly?

For every connected app in your smartphone, there is a single or set of corresponding servers providing the requested data like the weather of today. In the computing field, that smartphone app would be classified as the client-side while the server would be classified as the server-side.

Since the _Web 2.0_, it is possible to host any app on the web by-passing all the issues regarding software distribution, compatibility matters, and so on. These solutions are known as _Web Apps_ which can be executed from any browser regardless of the used device.

On the opposite, native mobile apps are run locally on the smartphones. The main advantages are that:
- more reachable for users since the launch icon is on their screens
- push notifications
- fluent user experience

However, there is a relatively recent approach that allows Web Apps to emulate the advantages of native apps. That solution is the use of <abbr title="Progressive Web Apps">PWA</abbr>. Although Apple has been known for having a deterrent policy for PWA by obstructing their distribution on iOS devices.

Because of the advantages of native mobile apps and the policy of Apple, I considered that native mobile apps were the approach to follow for my use case.

Besides all of this, in order to commit to a serious software project, it is required to use a _version control_ system, even if it is a solo project. This strategy consists on taking snapshots during the development in an iterative way. Each snapshot is known as a commit in which it is described the changes. Optionally, these snapshots can be uploaded to a remote repository, like _Github_, in order to store and share if desired all the progress.

There are many reasons to use it, but the main benefits are:
- it is a back-up to prevent data loss
- you can revert a change that causes problems
- it helps you track root-cause errors
- it is an effective way to work collaboratively with other contributors

## Client-side

How does an app deal with the different possible states? The pattern design of native mobile apps are based in Finite-state machines (FSM). In which, by definition, they react to the given conditions by changing their state.

Now, in terms of code implementation, you could just start typing lines of code without following a certain architecture and strategy. This is a terrible mistake. Don't do it. Otherwise, you could face some of the following troubles:
- collaborators will have a hard time trying to understand the underlying logics
- increased difficulty spotting bugs
- increased complexity in adding new features
- additional boilerplate because of the lack of re-usability

The point is to design from the ground scalable, modulated and maintainable code. It might require some discipline, but it is an invested with guaranteed returns.

In my case, although there are another approaches and there is no consensus about which one is better, I decided to leverage on the [<abbr title="Business Logic of Component">BLoC</abbr>](https://bloclibrary.dev/#/) pattern. 

> The strategy is to split the <abbr title="Application Programming Interface">API</abbr>, the business logic and the <abbr title="User Interface">UI</abbr> in different modules

<br>
{{< figure align="center" src="images/bloc.png" title="BLoC Design Pattern" >}}

Let's imagine you press the ***login*** button of an app, that would be an event whose associated logic is managed by the bloc component which subsequently requests an action to the server-side via the API (or _data_ as depicted in the image). Then, the server-side responses the data to the bloc module which triggers a new state that updates the UI, like the transition from the login screen to loading screen. And, then, to the main screen of the app.

In this post, there is no information about the underlying concepts of BLoC as it is out of the writing scope, but it is important to mention.

---

Okay, so, how did I actually implement all of this?

The current market context reveals there is a duopoly of two variants of smartphones regarding their <abbr title="Operating System">OS</abbr>: Android and iOS.
<p></p>

| Smartphone Variant | Android | iOS |
| --- | --- | --- |
| Native Programming Language | Java / Kotlin | Swift |
| Global Market Share as for 2021 [^2] | 75 % | 25 % |

This market share depends on the country, population sector, social status, and other factors. For the niche of market the social network was about to be implemented, surprisingly, it was revealed that the Android / iOS ratio is around 1:1. 

In my case, since I was bootstrapping, I had limited resources, no previous mobile programming experience, no outsourcing work, no hired programmers and time against the clock. If I only focused on learning one programming language, I would be discarding half of the potential users. That is certainly a problem. Thus, what option do I have? Do I really need to learn two different programming languages and create a parallel workflow?

> Fortunately, there are some solutions free to use that with a single programming language you can create apps for both iOS and Android.

My personal decision was the [Flutter](https://flutter.dev/) framework, which operates with [Dart](https://dart.dev/) as programming language. With a single codebase, allows scalability to:
- mobile apps
- web apps
- desktop apps
- embedded apps

<p></p>
{{< figure align="center" src="images/flutter.svg" >}}
{{< figure align="center" src="images/dart-logo.png" >}}

Besides, it is an open source project. This provides transparency and allows collaborators all around the world to contribute, so that, the project becomes more versatile, robust and scalable. One of the possible ways to leverage on the contributions of people / organizations is via [pub.dev](https://pub.dev/), a platform which allows reusing useful widgets for a wide range of purposes and publish your solutions.

The key concept of _Flutter_ is that every component or class is known as a _widget_. With these components you can build up an entire production-ready application, like if they were bricks supposed to be piled up to build a house.

## Server-side

The development of a mobile app that requires to be synchronized with an online database cannot be decoupled from the backend development. This means you either need to get to operate physical servers on your own or outsource that setup for the cloud services.

For prototyping, the ideal approach is to outsource these services to a <abbr title="Backend-as-a-Service">BaaS</abbr>.
Currently, there is a wild range of cloud server alternatives. In my case, I chose [Firebase](https://firebase.google.com/) as it wraps a conglomerate of different useful services. Some of the features I had the opportunity to leverage were:
- Authentication
  - to provide credentials to every user
- Firestore database
  - to handle and store the online data
- Storage
  - to store files like images and PDFs
- Cloud functions
  - to externalize and centralize logic
- Hosting
  - to host websites
- Analytics
  - to gather data generated by users subject to further study

<p></p>
{{< figure align="center" src="images/server-client-interaction.jpg" title="Server-side with Client-side interactions" >}}

For the server-side programming, [NodeJS](https://nodejs.org/en/about/) is the programming language used to deploy code. For instance, sometimes it is required to implement code in the server node, as it would not be scalable or feasible to deploy it to the client-side.

<br>
{{< figure align="center" src="images/firebase.png" >}}
{{< figure align="center" width="20%" src="images/nodejs-logo.svg" >}}
<br>

The cost of the service is based on the Pay-as-you-Go model. In which the billing is associated proportionally to the use of their resources. At the initial stages, as usually there is little volume of demand, it is for free given the generous use threshold level. 

This is the main reason why the code quality should be optimized in order to avoid redundant server calls, poor data structure design, poor security rules and critical flaws like recursive functions that almost annihilated several startups.

{{< youtube Lb-Pnytoi-8 >}}

<br>
See Firebase cost plan [here](https://firebase.google.com/pricing).

# Software Deployment

Flutter can generate compatible apps for both iOS and Android as Dart is able to compile code into:
- Android native code which is based in:
  - Kotlin or
  - Java
- iOS native code which is based in:
  - Swift

<br>

{{< figure align="center" src="images/flutter-android-ios.jpg" title="Dart compilation possibilities diagram" >}}

<br>

>Given the fact that Apple has a very restrictive policy, in order to compile code for iOS devices from Flutter, you are required to do it from an Apple Computer (Mac)

Once the package is generated, the distribution is when takes place. The most optimal approach is to publish the app on the correspondent ecosystem public app stores. Currently, the main ones are Apple Store for iOS devices and Google Play for Android devices.

Both also provide opportunities for early stage apps to be publicly tested by users with the possibility of applying some filters such as:
- the total number of testers
- available countries
- certain users by:
  - providing their emails
  - sharing an invitation code

To be able to distribute on the Google Play, you are required to create a Google Developer account and make a single payment of €25. In the case of Apple Store, you are also required to get an Apple Developer account, but the difference is you are required to yearly subscribe to their program which means you have to pay €100 every year.

Once you are done with obtaining a developer account, you can access their consoles. From there you can manage everything and obtain very useful metrics.

<br>

{{< figure align="center" src="images/apple-store.jpg" title="Apple Store Console" >}}
{{< figure align="center" src="images/google-play.jpg" title="Google Play Console" >}}

<br>

These platforms take for granted that there is never a final version of any digital product. This is because there is always something to fix, something to improve or some new feature to add. That is when updates of new releases take place. Software maintenance is an intrinsic part of the lifecycle of any product and should always be active to optimize users experience based on their feedback.  

1. Launch on the store
2. Receive feedback (can be direct or indirect)
3. Learn from 2.
4. Create countermeasures
5. Deploy again on the stores via a new update release
6. Repeat 2.

# Results of my use case

The results were technically satisfying. I managed to create a social network with the following features:
- Sign up
- Sign in
- Sign out
- Private invite-only social network growth control
- Set profile picture
- Set banner picture
- Chat
- Post (text, picture, and, optionally, a PDF file) sharing with other users
- Content scroll pagination
- Comment on posts
- Likes
- Reviews
- Follow and unfollow for curated feed
- Discover
  - users
    - new users
    - most followed users
    - users by filters
  - posts
    - new posts
    - most liked posts
    - posts by filters
- Delete
  - post
  - user
  - comment
  - review
- Report for moderation
  - post
  - comment
  - review
- Provide feedback

{{< youtube 1itdCNS-TIQ >}}

# Website

When creating apps for Android and iOS, it is always wise to create as well a website. At least, a landing page in which summarize and describe the features and ideas about the app concept.

This simple action increases dramatically the confidence transmitted to potential users and acts as an autonomous system that can answer simple questions by creating a <abbr title="Frequent Questions & Answers">FAQ</abbr> section.

In my case, I just needed a simple static website. There is a wild range of possible solutions available on the Internet. You can either use a <abbr title="Software-as-a-Service">SaaS</abbr> like _Wordpress, Wix, Mobirise,_... or implement it by yourself via a static generator website like _Hugo, GatsbyJs,_... among the possible approaches. 

Personally, I decided to give a chance to [Mobirise](https://mobirise.com/), since can provide a free service as long as use the minimal content and is great for quick prototyping.

{{< figure align="center" width="30%" src="images/mobirise-logo.png" >}}
{{< figure align="center" src="images/mobirise-case.jpg" title="My use case of Mobirise" >}}

Once you finish the draft, it is possible to export the project into a public folder which contains the required files that compose a website. They are mainly of <abbr title="JavaScript">JS</abbr>, <abbr title="Cascading Style Sheets">CSS</abbr> and <abbr title="HyperText Markup Language">HTML</abbr> type.

With Firebase Hosting, it is possible to deploy that folder to make it available online. By default, provides a <abbr title="Uniform Resource Locator">URL</abbr> which might not be of your interest, in that case, you should also buy a domain and link it afterwards. An available domain can be purchased from around €10.

You can check out the result here: www.opetaya.com

# Conclusion

Nowadays, it is totally feasible to bootstrap a production-ready project without the need of hiring a team of software developers for the very initial stages or investing a considerable amount of money. This is true, as long as, you are able to commit to a long learning path and an infinite loop of trial and error. If you have a job, no worries, there is still the chance of working this as your hobby time. You will just need a lot of patience and perseverance.

However, creating a real product and launching it to the market is one of the most fulfilling sensations you can ever get.

Now I suggest you to consider that idea you have been thinking about and just do it. Try to create something new, and be bold enough to test it with real people. Create your own purpose.

This world needs creative people and bold solutions.

{{< figure src="images/just-do-it.gif" >}}

>Fortune favors the brave – Publius Terentius Afer

[^1]: Mistakes in the code that prevents the software to operate as expected
[^2]: [Statista source](https://www.statista.com/statistics/272698/global-market-share-held-by-mobile-operating-systems-since-2009/#:~:text=Market%20share%20of%20mobile%20operating%20systems%20worldwide%202012%2D2021&text=Android%20maintained%20its%20position%20as,of%20the%20global%20market%20share.)