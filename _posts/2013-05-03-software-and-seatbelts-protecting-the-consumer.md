---
layout: post
published: true
title: Software and seatbelts. Protecting the consumer
date: 2013-05-03 22:10:00
categories:
- web-development
tags:
- leadership
- quality

---

This blog post is a result of a discussion I read on Twitter among a number of developers who were debating whether Wordpress should be criticized for the quality of its codebase. One point raised was that since Wordpress is hugely popular it must be good enough for the common user. A counter point stated that far more should have been done to keep the codebase secure and modern on behalf of the common user.

---


It set me to thinking about our responsibility when releasing or selecting a product, so here is a blog post.

#### Seatbelts and Airbags

On January 1, 1968 the federal law requiring vehicles to ship with seatbelts went into effect. Obviously the nation had taken a look at the vehicular mortality rates and decided that seat belts would be a "best practice". The federal law mandated that all vehicles must follow the best practice.

In the early 1990s the airbag law was passed mandating that by 1997 all cars must ship with airbags. This too had been concluded to be a good preventative measure for violent crashes.

#### Software and its seatbelts

My thought is that we as developers that release software or provide software as a service have a responsibility to follow our own industry's best practices in order to **protect less knowledgeable users**. We don't have a governing body to demand compliance (neither do we want one) so we must be guided by the desire to protect those who are trusting us.

The best practices that affect our industry include items such as:

* Password hashing with salts. Never store user passwords as plain text, md5, etc.
* Don't email users their passwords. If you do this you have probably also broken rule 1.
* Don't violate a user's trust by abusing information they shared with you. (phone, email, or text spam for example)
* Battle tested input sanitation. If you roll your own as you code you will be vulnerable at some point.
* Login limiting. With known usernames and no login limits a bot is free to brute force your user accounts.
* Test your code. Prevent regressions in code you ship. Speed up development. Make it easier for other developers to work with your code.
* Separation of concerns. Don't place database logic in your html. Designers don't deserve that; developers don't deserve that.
* Code reuse. Use packages, write packages and share them. The more that conventions are used and packages are shared the easier one developer can pick up where another left off.

Some of the above points affect the end-users such as website visitors that sign up for an account. Others such as separation of concerns only affect the developers who will be working with the code. This is also important! Many business owners have built upon poorly organized software only to realize that they had nowhere to go once their business started becoming successful and they needed to scale or customize. Since they were already invested they may have ploughed ahead sinking much more labor into the project than necessary.

If you're a software consumer build on the correct platform to start with. If at all possible consult with someone knowledgeable to help you decide. If you didn't and think you got it wrong don't be afraid to ask advice now and switch to a different solution. The less time you spend beating a dead horse the better.

#### Change is hard

If you are leading a software project nothing worthwhile comes easy. The key is to push for the best, don't stagnate. If you are an industry leader do your best to implement and invent best practices. Push your users/developers to learn with you. If you don't you will end up with an entire community around you that is stale. Considering the speed of innovation on the web your solution from 10 years ago most likely needs to be rethought.

Changing may require a change in direction or a deep refactor. Only you can decide if this should be done to your own projects. However don't be afraid of pushing your community to advance. The alternative is a slow and bitter death by becoming irrelevant.

#### Psychology

Perhaps our default is to have a legacy mindset toward software. Humans often want to use something because it is familiar, popular, or convenient, not because it's the correct tool for the job. Wordpress is not always the answer, PHP is not always the answer, nor PyroCMS, nor Rails, nor Word, or even Windows or Mac. Look at a wide array of tools, try them out, ask people smarter than yourself.

Then when someone asks you to make "Wordpress into a corporate CMS" or to "recode PyroCMS in ASP.NET" you can explain why it should or should not be done instead of blindly hacking it to fit their vision. (those are actual customer requests by the way)
Make a difference

If you are a developer or designer and you see an open source project lacking then see if you can pitch in. In many cases it will be welcomed. Newish projects may very well not be implementing best practices due to a lack of manpower or money. Older projects may reject help because they aren't willing for breaking changes. In the latter case it must wait on the leadership's vision and desire to push their community.

If you are an integrator or freelancer that advises others refuse to use software that doesn't measure up. Kindly educate others on deficiencies that have been swept under the rug for years. By continuing to say "it's not right but it's good enough" we are furthering the adoption of bad practices.

Lastly nobody is perfect. Approach everybody with respect and the initial assumption that they are doing their best.
