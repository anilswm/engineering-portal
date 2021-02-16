---
title: How to do full-text search in MongoDB
date: "2021-02-16"
coverImage: "coverImage.png"
author: "Anil Gupta"
Description: In this blog, we will learn how we can full-text search in MongoDB using text index.
tags: ["MongoDB"]
---

Full text search is very important feature when we talk about the finding content on internet. A google search is the best example for this when we find the content using the phrases or keywords. In this article we will learn about full-text search capebilities based on text index.

## Create a Sample Database

Before the start, we will create a sample Database that we will use for all examples in this tutorials.

We will create a database with name _myDB_ and will create a collection with name _books_. For this, the statement would be as follows.


```
> use myDB
> db.createCollection("books")
>
```

Let's insert some documents by using the following statement.

```
> db.books.insert([
	{
      "title": "Eloquent JavaScript, Second Edition",
      "subtitle": "A Modern Introduction to Programming",
      "author": "Marijn Haverbeke",
      "publisher": "No Starch Press",
      "description": "JavaScript lies at the heart of almost every modern web application, from social apps to the newest browser-based games. Though simple for beginners to pick up and play with, JavaScript is a flexible, complex language that you can use to build full-scale applications."
    },
    {
      "title": "Learning JavaScript Design Patterns",
      "subtitle": "A JavaScript and jQuery Developer's Guide",
      "author": "Addy Osmani",
      "publisher": "O'Reilly Media",
      "description": "With Learning JavaScript Design Patterns, you'll learn how to write beautiful, structured, and maintainable JavaScript by applying classical and modern design patterns to the language. If you want to keep your code efficient, more manageable, and up-to-date with the latest best practices, this book is for you."
    },
    {
      "title": "Speaking JavaScript",
      "subtitle": "An In-Depth Guide for Programmers",
      "author": "Axel Rauschmayer",
      "publisher": "O'Reilly Media",
      "description": "Like it or not, JavaScript is everywhere these days-from browser to server to mobile-and now you, too, need to learn the language or dive deeper than you have. This concise book guides you into and through JavaScript, written by a veteran programmer who once found himself in the same position."
    },
    {
      "title": "Programming JavaScript Applications",
      "subtitle": "Robust Web Architecture with Node, HTML5, and Modern JS Libraries",
      "author": "Eric Elliott",
      "publisher": "O'Reilly Media",
      "description": "Take advantage of JavaScript's power to build robust web-scale or enterprise applications that are easy to extend and maintain. By applying the design patterns outlined in this practical book, experienced JavaScript developers will learn how to write flexible and resilient code that's easier-yes, easier-to work with as your code base grows."
    },
    {
      "title": "Understanding ECMAScript 6",
      "subtitle": "The Definitive Guide for JavaScript Developers",
      "author": "Nicholas C. Zakas",
      "publisher": "No Starch Press",
      "description": "ECMAScript 6 represents the biggest update to the core of JavaScript in the history of the language. In Understanding ECMAScript 6, expert developer Nicholas C. Zakas provides a complete guide to the object types, syntax, and other exciting changes that ECMAScript 6 brings to JavaScript."
    },
    {
      "title": "You Don't Know JS",
      "subtitle": "ES6 & Beyond",
      "author": "Kyle Simpson",
      "publisher": "O'Reilly Media",
      "description": "No matter how much experience you have with JavaScript, odds are you don’t fully understand the language. As part of the "You Don’t Know JS" series, this compact guide focuses on new features available in ECMAScript 6 (ES6), the latest version of the standard upon which JavaScript is built."
    },
    {
      "title": "Git Pocket Guide",
      "subtitle": "A Working Introduction",
      "author": "Richard E. Silverman",
      "publisher": "O'Reilly Media",
      "description": "This pocket guide is the perfect on-the-job companion to Git, the distributed version control system. It provides a compact, readable introduction to Git for new users, as well as a reference to common commands and procedures for those of you with Git experience."
    },
    {
      "title": "Designing Evolvable Web APIs with ASP.NET",
      "subtitle": "Harnessing the Power of the Web",
      "author": "Glenn Block, et al.",
      "publisher": "O'Reilly Media",
      "description": "Design and build Web APIs for a broad range of clients—including browsers and mobile devices—that can adapt to change over time. This practical, hands-on guide takes you through the theory and tools you need to build evolvable HTTP services with Microsoft’s ASP.NET Web API framework. In the process, you’ll learn how design and implement a real-world Web API."
    }
])
```

## Creating a Text Index

We need to create a text index on the fields to perform the text search. Text index can be created on single or multiple fields. The following statement will create a text index on single field.
```
>db.books.createIndex({"description":"text"})
```
For this tutorial we will create text index on _description_ and _subtitle_ fields. We can create only one text index per collection in MongoDB. So We will create a compound text index using the following statement.
```
>db.books.createIndex({"subtitle":"text","description":"text"})
```

### $search
Now we will try to search documents that have the keywords 'ECMAScript' in _description_ and _subtitle_ fields. For this we can use below statement.

```
db.books.find({$text: {$search: "ECMAScript"}})
```
**Example**
```
>db.books.find({$text: {$search: "ECMAScript"}},{ subtitle: 1, description: 1 })
	{
    "_id" : ObjectId("602b09cb3cb6144ada1c62fe"),
    "subtitle" : "The Definitive Guide for JavaScript Developers",
    "description" : "ECMAScript 6 represents the biggest update to the core of JavaScript in the history of the language. In Understanding ECMAScript 6, expert developer Nicholas C. Zakas provides a complete guide to the object types, syntax, and other exciting changes that ECMAScript 6 brings to JavaScript."
	}
>
```
#### Phrases

We can search for phrases using the text index. By deafult text serach perform a OR search for all words in the phrase. If we want to serach 'modern design patterns' then it will search for documents that have the keywords either modern, design or patterns.

**Example**
```
>db.books.find({$text: {$search: "modern design patterns"}},{ subtitle: 1, description: 1 })
	{
    "_id" : ObjectId("602b098f3cb6144ada1c2ea1"),
    "subtitle" : "A JavaScript and jQuery Developer's Guide",
    "description" : "With Learning JavaScript Design Patterns, you'll learn how to write beautiful, structured, and maintainable JavaScript by applying classical and modern design patterns to the language. If you want to keep your code efficient, more manageable, and up-to-date with the latest best practices, this book is for you."
	},
	{
    "_id" : ObjectId("602b09b93cb6144ada1c4bca"),
    "subtitle" : "Robust Web Architecture with Node, HTML5, and Modern JS Libraries",
    "description" : "Take advantage of JavaScript's power to build robust web-scale or enterprise applications that are easy to extend and maintain. By applying the design patterns outlined in this practical book, experienced JavaScript developers will learn how to write flexible and resilient code that's easier-yes, easier-to work with as your code base grows.",
	},
	{
    "_id" : ObjectId("602b095c3cb6144ada1c1028"),
    "subtitle" : "A Modern Introduction to Programming",
    "description" : "JavaScript lies at the heart of almost every modern web application, from social apps to the newest browser-based games. Though simple for beginners to pick up and play with, JavaScript is a flexible, complex language that you can use to build full-scale applications."
	}
>
```
In case you would like to perform an exact phrase search (logical AND), you can do so by specifying double quotes in the search text.
 
**Example**
```
>db.books.find({$text: {$search: "\"modern design patterns\""}},{ subtitle: 1, description: 1 })
	{
    "_id" : ObjectId("602b098f3cb6144ada1c2ea1"),
    "subtitle" : "A JavaScript and jQuery Developer's Guide",
    "description" : "With Learning JavaScript Design Patterns, you'll learn how to write beautiful, structured, and maintainable JavaScript by applying classical and modern design patterns to the language. If you want to keep your code efficient, more manageable, and up-to-date with the latest best practices, this book is for you."
}
>
```
