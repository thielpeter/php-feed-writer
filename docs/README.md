# Feed Writer

<img src="https://lukaswhite.github.io/php-feed-writer/assets/php-feed-writer.svg" width="120px" alt="PHP Feed Writer">

A PHP library for writing feeds. Currently supports RSS 2.0, Atom and iTunes, along with support for MediaRSS, Dublin Core, GeoRSS and WordPress eXtended RSS (WXR).

In other words, you could use it for:

* Creating RSS and Atom feeds
* Creating podcasts
* Publishing videos
* Creating audio feeds
* Creating lists of geographical places
* ...and more

[![Build Status](https://travis-ci.org/lukaswhite/php-feed-writer.svg?branch=master)](https://travis-ci.org/lukaswhite/php-feed-writer)

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/3a85bb8ef0af464382fb8acae9a6bd55)](https://www.codacy.com/app/lukaswhite/php-feed-writer?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=lukaswhite/php-feed-writer&amp;utm_campaign=Badge_Grade)

## Features

* Modern (PHP7.1+)
* Flexible; use it for syndication, media, podcasts...
* Fast
* Easy to extend
* Supports custom namespaces
* Full MediaRSS support
* Dublin Core support
* GeoRSS support
* Supports XSL stylesheets
* No third-party dependencies
* Fully tested

## Simple Examples

Sometimes a library is best introduced with some simple examples. Note that these don't demonstrate the full range of capabilities.

### RSS

```php
$feed = new RSS2( );

$channel = $feed->addChannel( );

$channel
    ->title( 'My Blog' )
    ->description( 'My personal blog' )
    ->link( 'https://example.com' )
    ->lastBuildDate( new \DateTime( ) )
    ->pubDate( new \DateTime( ) )
    ->language( 'en-US' );

foreach( $posts as $post ) {
    $channel->addItem( )
        ->title( $post->title )
        ->description( $post->description )
        ->link( $post->url )
        ->pubDate( $post->publishedAt )
        ->guid( $post->url, true );
}

print $feed;
```

### RSS in Laravel

```php
$feed = new RSS2( );

// ...

return response( )->make(
    $feed->toString( ),
    200,
    [
        'Content-Type' => $feed->getMimeType( ),
    ]
);

```

### Atom

```php
$feed = new \Lukaswhite\FeedWriter\Atom( );

$feed->title( 'Example Feed' )
    ->link( 'http://example.org/' )
    ->updated( new \DateTime( '2003-12-13T18:30:02Z' ) )
    ->id( 'urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6' );
    
foreach( $posts as $post ) {
    $feed->addEntry( )
        ->title( $post->title )
        ->id( $post->id )
        ->updated( $post->updatedAt );
}

print $feed;
```

### iTunes

```php
$feed = new Itunes( );

$channel = $feed->addChannel( );

$channel->title( 'All About Everything' )
    ->subtitle( 'A show about everything' )
    ->description( 'All About Everything is a show about everything. Each week we dive into any subject known to man and talk about it as much as we can. Look for our podcast in the Podcasts app or in the iTunes Store' )
    ->summary( 'All About Everything is a show about everything. Each week we dive into any subject known to man and talk about it as much as we can. Look for our podcast in the Podcasts app or in the iTunes Store' )
    ->link( 'http://www.example.com/podcasts/everything/index.html' )
    ->image( 'http://example.com/podcasts/everything/AllAboutEverything.jpg' )
    ->author( 'John Doe' )
    ->owner( 'John Doe', 'john.doe@example.com' )
    ->explicit( 'no' )
    ->copyright( '&#x2117; &amp; &#xA9; 2014 John Doe &amp; Family' )
    ->generator( 'Feed Writer' )
    ->ttl( 60 )
    ->lastBuildDate( new \DateTime( '2016-03-10 02:00' ) );

$channel->addItem( )
    ->title( 'Shake Shake Shake Your Spices' )
    ->author( 'John Doe' )
    ->subtitle( 'A short primer on table spices' )
    ->duration( '07:04' )
    ->summary( 'This week we talk about <a href="https://itunes/apple.com/us/book/antique-trader-salt-pepper/id429691295?mt=11">salt and pepper shakers</a>, comparing and contrasting pour rates, construction materials, and overall aesthetics. Come and join the party!' )
    ->pubDate( new \DateTime( '2016-03-08 12:00' ) )
    ->guid( 'http://example.com/podcasts/archive/aae20140615.m4a' )
    ->explicit( 'no' )
    ->addEnclosure( )
        ->url( 'http://example.com/podcasts/everything/AllAboutEverythingEpisode3.m4a' )
        ->length( 8727310 )
        ->type( 'audio/x-m4a' );
        
print $feed;
```

## Installation

This package requires PHP 7.1+.

Install the package using [Composer](https://getcomposer.org/):

```
composer require lukaswhite\php-feed-writer
```

## RSS

### Creating a Feed

To create a feed:

```php
use Lukaswhite\FeedWriter\Feed;

$feed = new RSS2( );
```

This creates a feed with the `utf-8` character encoding; to override that:

```php
$feed = new RSS2( 'iso-8859-1' );
```

#### Feed Namespaces

By default, the `content` namespace is set (`http://purl.org/rss/1.0/modules/content/`).

To register a new namespace:

```php
$feed->registerNamespce( 'dc', 'http://purl.org/dc/elements/1.1/' );
```

For convenience, the following common namespaces have their own corresponding methods:

```php
$feed->registerAtomNamespace( );
$feed->registerMediaNamespace( );
$feed->registerDublinCoreNamespace( );
$feed->registerGeoRSSNamespace( );
```

To check whether a namespace is registered:

```php
$registered = $feed->isNamespaceRegistered( 'name' );
```

To unregister a namespace &mdash; for example, if for whatever reason you don't want the `content` namespace set &mdash; simply do this:

```php
$channel->unregisterNamespace( 'content' );
```

Note that when you add an Atom link then the appropriate namespace is automatically added, and likewise media, GeoRSS or Dublin Core elements.

Of course, you're free to add [your own custom namespaces](https://www.disobey.com/detergent/2002/extendingrss2/).

### Creating a Channel

A call to `addChanel` creates a new channel, adds it to the feed and returns it.

```php
$channel = $feed->addChannel( );
```

From there, the `Channel` class provides a number of methods for settings its attributes using a predominantly fluent interface.

#### Setting a Channel's Title

To set the title of the channel (required):

```php
$channel->title( 'My Blog' );
```

#### Setting a Channel's Description

To set a description of the channel:

```php
$channel->description( 'My personal blog' );
```

If you want the description to be character encoded &mdash; i.e. wrapped in a `CDATA` section &mdash; then pass `true` as a second argument. If you don't, it will attempt to guess whether it should do so, by attempting to detect HTML in the provided description.

#### Setting a Channel's Link

To specify a link for the channel:

```php
$channel->link( 'https://example.com' );
```

#### Setting a Channel's Language

Set the language like this:

```php
$channel->language( 'en-GB' );
```

#### Setting a Channel's Copyright Notice

Add a copyright notice as follows:

```php
$channel->copyright( 'Copyright 2018 Me' );
```

#### Setting a Channel's Last Build Date

To specify when the channel was last built, pass an instance of `\DateTime`:

```php
$channel->lastBuildDate( new \DateTime( ) );
```

> If you use the excellent [Carbon](https://carbon.nesbot.com/docs/) library then, since it's an extension of `\DateTime`, you can simply pass an instance of that. It's not a required package, however.

#### Setting a Channel's Published Date

To specify when the channel was published, pass an instance of `\DateTime`:

```php
$channel->pubDate( new \DateTime( ) );
```

> If you use the excellent [Carbon](https://carbon.nesbot.com/docs/) library then, since it's an extension of `\DateTime`, you can simply pass an instance of that. It's not a required package, however.

#### Setting a Channel's Categories

Call `addCategory()` on the channel object to add a category. This returns an instance of `Category` which includes methods to set the name and, optionally, the domain. For example:

```php
$channel->addCategory( )
	->name( 'PHP' )
	->domain( 'the domain' );
```


#### Adding Links

You can add a link to the feed like this:

```php
$channel->addLink(
    'atom:link',
    'https://example.com/feed.xml',
    'self', // the rel attribute
    'application/atom+xml'
);
```

For convenience, you can add an Atom link as follows:

```php
$channel->addAtomLink( 'https://example.com/feed.xml' );
```

This will automatically register the appropriate namespace for you.

#### PubSubHubbub

To add a PubSubHubbub link:

```php
$channel->addPubSubHubbubLink( 'http://pubsubhubbub.appspot.com' );
```

If you need more flexibility, just use `addLink` directly. For example the line above does this:

```php
$this->addLink(
    'atom:link',
    'http://pubsubhubbub.appspot.com',
    'hub'
);    
```

#### Adding Images

You can add an image to a channel by calling `addImage()`:

```php
->addImage( )
    ->url( 'http://example.com/image.jpeg' )
    ->link( 'http://example.com' )
    ->title( 'An example image')
    ->width( 200 )
    ->height( 150 );
```

> You can also add images using MediaRSS, which provides a whole host more options. Which one you choose, however, may ultimately come down to the constraints on the feed readers that you anticipate will read your feed.

#### Setting the Generator

The generator specifies the software used to generate the feed:

```php
$channel->generator( 'PHP Feed Writer (https://github.com/lukaswhite/php-feed-writer)' )
```

> If you find this package useful, please consider setting this accordingly!

#### Other Channel Properties

There are a few other miscellaneous properties of the channel object [defined in the spec](http://www.rssboard.org/rss-profile#element-channel) and implemented for completeness. All are optional, and may or may not be observed by various feed readers.

```php
$channel->author( 'author@example.com' )
    ->webmaster( 'webmaster@example.com' )
    ->managingEditor( 'editor@example.com (the editor)' )
    ->rating( '(PICS-1.1 "http://www.rsac.org/ratingsv01.html" l by "webmaster@example.com" on "2007.01.29T10:09-0800" r (n 0 s 0 v 0 l 0))' )
    ->skipDays( 'Saturday', 'Sunday' )
    ->skipHours( 0, 1, 2, 3, 4 )
    ->ttl( 60 );
```

> Note that the `author`, `webmaster` and `managingEditor` elements must contain an e-mail address, not just a name.

### Adding Items

Now that your feed has a channel, it's time to start adding items.

To add an item to the channel, call `addItem()`. This creates a new instance, attaches it to the feed and returns it. Like channels, the `Item` class has a fluent interface that you can use to set the appropriate properties.

#### Setting the Item's title

To set the all-important title of an item:

```php
$channel->addItem( )
    ->title( 'A Blog Post' );
```    

#### Setting the Item's description

You can provide a description of an item like this:

```php
$item->description( 'Just a post, on a blog' );
```    

> This behaves in the same way as the channel descripton with respect to character encoding; see the section on that for details.

#### Setting the Item's Link

You'll need to provide a link to the item:

```php
$item->link( 'http://example.com/blog/post-1.html' )
```    

#### Setting the GUID of the Item

All items must have a GUID (Globally Unique Identifier), for example in the form of a URI. In the case of a blog post, this will simply be a link to the post in question.

```php
$item->guid( 'http://example.com/blog/post-1.html' )
```    

If the GUID is a permalink, pass `true` as the second argument:

```php
$item$entry->guid( 'http://example.com/blog/post-1.html' )
```    

The result will be:

```xml
<guid isPermalink="true">http://example.com/blog/post-1.html</guid>
```

#### Setting Item's Published Date

Pass an instance of `\DateTime` to specify when an item was published.

```php
$item->pubDate( new \DateTime( '2018-09-07 09:30' ) );
```

#### Setting the Content of an Item

RSS2.0 allows you to incoporate the full content of an item; for example the whole of a blog post.

If you're incoporating HTML content, then that would go in the encoded content, which you can add this as follows:

```php
$item->encodedContent( '<p>The content of the item</p>' );
```

> From the spec; "the content MUST be suitable for presentation as HTML"

#### Adding Enclosures

To add an enclosure; for example a link to an item of audio:

```php
$item->addEnclosure( )
    ->url( 'http://example.com/audio.mp3' )
    ->length( 1000 )
    ->type( 'audio/mpeg' );
```    

> MediaRSS, described later, provides another way to do this but with a larger range of parameters and additional information. The decision as to which to use may come down to the limitations or features of the feed readers that ultimately consume your feeds.

#### Adding Media

Please see the section on MediaRSS.

### Getting the Feed

Once you've built a feed, you'll need to do something with it.

Most commonly, you'll call `toString()` on the feed object which, as the name suggests, returns a string representation of the feed &mdash; i.e. a string of XML.

```php
print $feed->toString( );
```

Feeds also implement the magic `__toString()` method, so this is the same:

```php
print $feed;
```

You can specify that you want the result to be pretty-printed (formatted);

```php
$feed->prettyPrint( );
print $feed->toString( );
```

If you wish to make any modifications to the feed XML before printing it, calling `build( )` will return an instance of `DomDocument`.

Finally for convenience, the feeds all implement a method named `getMimeType()`  which returns the MIME type that you should use to deliver the feed over HTTP.

```php
$feed = new RSS2( );
print $feed->getMimeType( ); // prints application/rss+xml
```

Here's a Laravel example of returning a feed, setting the appropriate header:

```php
return response( )->make(
    $feed->toString( ),
    200,
    [
        'Content-Type' => $feed->getMimeType( ),
    ]
);
```

## Atom

You can use this library to create Atom feeds.

### Creating a Feed

To create an Atom feed:

```php
$feed = new Atom( );
```

By default, the character encoding is set to `utf-8`; however you can override that like this:

```php
$feed = new Atom( 'iso-8859-1' );
```

### Required Feed Elements

#### Setting the Feed's Title

To set the title of the feed:

```php
$feed->title( 'Example Feed' );
```

The method takes an optional second parameter to specify the type; e.g. `plain` (the default) or `html`. [Click here](https://validator.w3.org/feed/docs/atom.html#text) for an explanation.

For example:

```php
$feed->title( 'Example <strong>Feed</strong>', 'html' );
```

#### Setting the Feed's ID

Atom feeds require an ID. From the spec: [the ID] identifies the feed using a universally unique and permanent URI. You can also use a URN ([Uniform Resource Name](https://en.wikipedia.org/wiki/Uniform_Resource_Name)) if you prefer.

```php
$feed->id( 'urn:uuid:60a76c80-d399-11d9-b93C-0003939e0af6' );
```

#### Specifying when the Feed was Updated

The third and final required piece of information on an Atom feed is "the time that the feed was last modified in a significant way":

```php
$feed->updated( new \DateTime( '2003-12-13T18:30:02Z' ) );
```

### Recommended Feed Elements

#### Specifying the Feed's Author(s)

You can specify one or more authors of a feed by calling the `addAuthor()` method, which returns an instance of a class named `Person`. [Refer to the spec](https://validator.w3.org/feed/docs/atom.html#person) for an explanation.

Once you have that instance you can set the name, e-mail address or / and a URI using the following methods:

```php
$feed->addAuthor( )
    ->name( 'John Doe' )
    ->email( 'john@doe.com' )
    ->uri( 'http://doe.com/john' );
```

#### Feed Links

You can add one or more links to a channel. A link is an entity, with a corresponding class, [as per the spec](https://validator.w3.org/feed/docs/atom.html#link). As such you can call a number of methods on an instance of `Link` to set the `url`, `rel` attribute, `type`, the language of the referenced resource (`hreflang`), the `title` or the `length`.

You should add a link back to the feed itself:

```php
$feed
    ->addLink( )
        ->url( '/feed' )
        ->rel( 'self' );
```        

Or use the short-cut method:

```php
$feed->addLinkToSelf( 'http://example.org/feed' )
```
        
To add a link to a related webpage, you might do this:

```php
$feed
    ->addLink( )
        ->url( 'http://www.example.com' )
        ->rel( 'alternate' );
```        

### Optional Feed Elements

There are a number of additional elements [defined by the spec](https://validator.w3.org/feed/docs/atom.html#optionalFeedElements).

#### The Feed's Categor(ies)

A feed can have one or more categories.

Like `Person` and `Link`, a `Category` is also an object; refer to the relevant section [in the spec](https://validator.w3.org/feed/docs/atom.html#category). Thus, calling `addCategory()` will return an object; you can then call methods on that to set various properties.

Here's the method at its simplest:

```php
$feed->addCategory( )
    ->term( 'example' );
```

A category may also have a `scheme` and/or a `label`:

```php
$feed->addCategory( )
    ->term( 'example' )
    ->scheme( 'http://example.com/categories/')
    ->label( 'An example' );    
```

#### Contributor(s) to a Feed

In addition to authors, you may also specify contributors. Like `addAuthor()`, this is modelled by the `Person` class.

To illustrate with an example:

```php
$feed->addContributor( )
    ->name( 'Jane Doe' )
    ->email( 'jane@doe.com' )
    ->uri( 'http://doe.com/jane' );
```

#### The Generator    

The generator is the software used to generate the feed, and in addition to a name may specify a related URI and / or a version number.

Just the name:

```php
$feed->generator( 'PHP Feed Writer' );
```            

Including a URI and version:

```php
$feed->generator(
    'PHP Feed Writer',
    'https://github.com/lukaswhite/php-feed-writer',
    '1.0' 
);
```            

If you find this package useful, please consider setting it!

#### Icon

To associate an icon with a feed:

```php
$feed->icon( 'http://example.org/icon.png' );
```

> Icons should be square

#### Feed Image

The image "identifies a larger image which provides visual identification for the feed.".

```php
$feed->image( 'http://example.org/image.png' );
```

> Images should be twice as wide as they are tall.

#### Rights

Rights just means a copyright notice. It may contain HTML, provided you indicate that when setting it.

Plain text:

```php
$feed->rights( 'Copyright 2018 Example' );
```

With HTML:

```php
$feed->rights( '&amp;copy; 2005 John Doe', 'html' );
```

#### Subtitle

You can also add a subtitle to a feed:

```php
$feed->subtitle( 'Just an example' );
```

Like the `title()` method takes an optional second argument that specifies the type, so be sure to set it to `html` should your subtitle contain HTML.

### Atom Feed Entries

Once you've created your Atom feed and set the relevant information, it's time to start adding entries.

A number of the methods on the `Entry` class are the same as feeds, as are the concepts of the various entites; for example `Person`, `Category` and `Link`. Like feeds, a number of the text-based elements can also have an associated type including, but not limited to, `plain` (the default) and `html`.

To add an entry:

```php
$entry = $feed->addEntry( );
```

This returns an instance of the `Entry` class, many of whose methods adopt a fluent interface.

### Required Entry Elements

An entry has three required elements; the `title`, `id` and `updated`.

#### Setting an Entry's Title

To set the title of an entry:

```php
$entry->title( 'Atom-Powered Robots Run Amok' );
```

If your title contains HTML:

```php
$entry->title( 'Atom-Powered Robots <strong>Run Amok</strong>', 'html' );
```

#### Specifying the ID of an Entry

The `id` element uniquely identifies an Atom feed's entry. It's usually the URI that represents the entry, but you can use whatever you like; for example, you can use a URN ([Uniform Resource Name](https://en.wikipedia.org/wiki/Uniform_Resource_Name)).

```php
$entry->id( 'urn:uuid:1225c695-cfb8-4ebb-aaaa-80da344efa6a' );
```

#### Setting the `updated` Element

The third and final required element on an Atom Entry is `updated`, which indicates the last time the entry was modified in a significant way. 

From the spec; "this value need not change after a typo is fixed, only after a substantial modification. Generally, different entries in a feed will have different updated timestamps."

To set it, simply pass an instance of `DateTime` to the `updated()` method:

```php
$entry->updated( new \DateTime( '2003-12-13T18:30:02Z' ) )
```

### Recommended Entry Elements

There are anumber of elements that the spec [recommends](https://validator.w3.org/feed/docs/atom.html#recommendedEntryElements) that you incorporate into your entries.

#### Authors

You can specify one or more authors of an entry in exactly the same way as for feeds.

```php
$entry->addAuthor( )
    ->name( 'John Doe' )
    ->email( 'john@doe.com' )
    ->uri( 'http://doe.com/john' );
```

#### Content

An Atom feed entry may contain the actual content of an item. Note that if you don't provide a link then you must set the content.

```php
$entry->content( 'The content of the entry' );
```

You can use HTML:

```php
$entry->content( 'The content of the <strong>entry</strong>', 'html' );
```

You'll find more information about content [here](https://validator.w3.org/feed/docs/atom.html#contentElement).

#### Links

Generally a link in an entry represents a related web page. Like feeds, links are modelled using the `Link` class, which in turn represents the `Link` construct [defined in the spec](https://validator.w3.org/feed/docs/atom.html#link).

Here's a simple example:

```php
$entry
    ->addLink( )
    ->url( 'http://example.org/2003/12/13/atom03' );
```

You may also use the `rel()`, `type()` and `language()` methods if you wish.

#### Entry Summary

You can also provide a short summary, abstract, or excerpt of the entry using the `summary()` method.

```php
$entry->summary( 'Short summary here' );
```

Again, the method has an optional second parameter that specifies the type, for example:

```php
$entry->summary( 'Short <strong>summary</strong> here' );
```

### Optional Entry Elements

There are a few other elements you can add to an entry that are optional.

#### Categories

Categories work in exactly the same way as feeds, so in other words in addition to the term itself they may have a `term`, `scheme` and / or a `label`.

At its simplest:

```php
$entry->addCategory( )
    ->term( 'example' );
```

Adding additional information about the category:

```php
$entry->addCategory( )
    ->term( 'example' )
    ->scheme( 'http://example.com/categories/')
    ->label( 'An example' );
```

#### The Publication Date

The `published` element represents the date and time an entry was initially created.

Set it in the same way as the `updated` element; i.e. with an instance of `DateTime`.

```php
$entry->published( new \DateTime( '2003-12-11T18:30:02Z' ) );
```

#### Rights

Rights refers to the copyright notice, and works in exactly the same way as the feed.

Plain text:

```php
$entry->rights( 'Copyright 2018 Example' );
```

With HTML:

```php
$entry->rights( '&amp;copy; 2005 John Doe', 'html' );
```

#### Source

If an entry is a copy then the `source` element provides metadata about the original.

To add a source:

```php
$entry->addSource( )
    ->id( 'http://example.org/' )
    ->title( 'Example, Inc.' )
    ->updated( new \DateTime( '2003-12-13T18:30:02Z' ) );
```

### Adding Enclosures to Atom Entries

To add an enclosure to an Atom entry:

```php
$entry->addEnclosure( )
    ->url( 'http://example.com/audio.mp3' )
    ->length( 1000 )
    ->type( 'audio/mpeg' );
```

Unlike RSS, where enclosures are presented by elements, in Atom an enclosure is actually a `<link>` that points to the fie in question, but with the `rel` attribute set to `enclosure`.    
        
### Feed Namespaces

Namespaces work in exactly the same way as RSS, except that the default namespace for an Atom feed is set to `http://www.w3.org/2005/Atom`.

### Getting the Feed

Once you've built a feed, you'll need to do something with it.

Most commonly, you'll call `toString()` on the feed object which, as the name suggests, returns a string representation of the feed &mdash; i.e. a string of XML.

```php
print $feed->toString( );
```

Feeds also implement the magic `__toString()` method, so this is the same:

```php
print $feed->toString( );
```

You can specify that you want the result to be pretty-printed (formatted);

```php
$feed->prettyPrint( );
print $feed->toString( );
```

If you wish to make any modifications to the feed XML before printing it, calling `build( )` will return an instance of `DomDocument`.

Finally for convenience, the feeds all implement a method named `getMimeType()`  which returns the MIME type that you should use to deliver the feed over HTTP.

```php
$feed = new Atom( );
print $feed->getMimeType( ); // prints application/atom+xml
```

Here's a Laravel example:

```php
return response( )->make(
    $feed->toString( ),
    200,
    [
        'Content-Type' => $feed->getMimeType( ),
    ]
);
```

## Dublin Core

[Dublin Core](https://feedforall.com/macdocs/html/RSSReferenceDC.html) is also supported. Here's an example:

```php
$channel
    ->dcTitle( 'Example feed' )
    ->dcCreator( 'creator@example.com' )
    ->dcContributor( 'contributor@example.com' )
    ->dcCoverage( 'United Kingdom' )
    ->dcDate( $date )
    ->dcDescription( 'An example feed' )
    ->dcFormat( 'text/html' )
    ->dcIdentifier ('http://example.com/feed.rss' )
    ->dcLanguage( 'en' )
    ->dcPublisher( 'Example Co' )
    ->dcRelation( 'http://example2.com' )
    ->dcRights( 'Copyright Example' )
    ->dcSource( 'http://example.com' )
    ->dcSubject( 'It is an example' )
    ->dcType( 'Text' );
```

> As soon as you add a Dublin Core tag, the namespace automatically gets registered for you.

## MediaRSS

[MediaRSS](http://www.rssboard.org/media-rss) is an extension of the RSS specification which allows you to incoporate media &mdash; such as images, videos and audio &mdash; in your RSS feeds. It's much more comprehensive than the image and enclosures provided by RSS.

You can add the following media types:

- Images
- Videos
- Audio
- Documents
- Executables

In addition to providing references to the media itself, you can provide all sorts of additional information, such as:

- Thumbnails
- Transcripts of videos
- Restrictions
- Comments
- Credits

### Bare-bones Example

In the following example, we're adding a video to an RSS item:

```php
$media = $item->addMedia( )
    ->url( 'http://www.webmonkey.com/monkeyrock.mpg' )
    ->medium( Media::VIDEO )
    ->fileSize( 2471632 )
    ->type( 'video/mpeg' );
);
```

### A More Comprehensive Example

In this example, we're adding a whole raft of additional information about a video:

```php
$media = $item->addMedia( )
    ->url( 'http://www.webmonkey.com/monkeyrock.mpg' )
    ->fileSize( 2471632 )
    ->type( 'video/mpeg' )
    ->title( 'The Webmonkey Band "Monkey Rock"' )
    ->description( 'See Rocking Webmonkey Garage Band playing our classic song "Monkey Rock" to a sold-out audience at the <a href="http://www.fillmoreauditorium.org/">Fillmore Auditorium</a>.', 'html' )
    ->keywords( 'monkeys', 'music', 'rock' )
    ->width( 320 )
    ->height( 240 )
    ->duration( 147 )
    ->medium( Media::VIDEO )
    ->expression( Media::FULL )
    ->bitrate( 128 )
    ->framerate( 24 )
    ->isDefault( )
    ->player( 'http://www.somevideouploadsite/webmonkey.html' )
    ->hash( 'dfdec888b72151965a34b4b59031290a', 'md5' )
    ->comments(
        'This is great',
        'I like this'
);
```

### MediaRSS Support in Detail

After those examples, it's time to dive deeper. There's a lot you can do with MediaRSS so let's start with the basics, then the rest is for reference.

#### Basic Information

To add media to a channel / feed / item / entry, call `addMedia()`. That returns a `Media` instance that you can begin to manipulate.

The first thing you'll need to do is set the Url of the actual media:

```php
$media->url( 'http://www.webmonkey.com/monkeyrock.mpg' );
```

Let's also set the MIME type and file size;

```php
$media->type( 'video/mpeg' )
    ->filesize( 2471632 );
```

We should also set the medium; that just specifies whether it's a video, image, audio, a document or an executable.

> You would expect that the media could be inferred from the MIME type, but [the specification](http://www.rssboard.org/media-rss) isn't clear on whether that's likely to be the case; so it's probably best to set it to make sure.

```php
$media->medium( Media::VIDEO );
```

#### Text-based Metadata

Media may also have a [title](http://www.rssboard.org/media-rss#media-title), [description](http://www.rssboard.org/media-rss#media-description) and [keywords](http://www.rssboard.org/media-rss#media-keywords):

```php
$media
    ->title( 'The Webmonkey Band "Monkey Rock"' )
    ->description( 'See Rocking Webmonkey Garage Band playing our classic song "Monkey Rock" to a sold-out audience at the Fillmore Auditorium.' )
    ->keywords( 'monkeys', 'music', 'rock' );
```

Note that the description also supports HTML, provided you pass `html` as the optional second argument:

```php
$media
    ->title( 'The Webmonkey Band "Monkey Rock"' )
    ->description( 'See Rocking Webmonkey Garage Band playing our classic song "Monkey Rock" to a sold-out audience at the <a href="http://www.fillmoreauditorium.org/">Fillmore Auditorium</a>.', 'html' )
    ->keywords( 'monkeys', 'music', 'rock' );
```

#### The Hash

You may provide a [hash](http://www.rssboard.org/media-rss#media-hash) for the binary media file. By default it's assumed that it's an `md5` hash, but `sha-1` is also supported; simply pass that as the second argument.

```php
$media->hash( 'dfdec888b72151965a34b4b59031290a' )
// is equivalent to
$media->hash( 'dfdec888b72151965a34b4b59031290a', 'md5' )
```

#### Categories

Media objects may have one or more [categories](http://www.rssboard.org/media-rss#media-category). Categories may have an optional scheme and / or label.

Here are three examples:

Just the term:

```php
$media->addCategory( )
    ->term( 'music/artist/album/song' );
```

> The default scheme is `http://search.yahoo.com/mrss/category_ schema`

With a custom scheme:

```php
$media->addCategory( )
    ->term( 'ycantpark mobile' )
    ->scheme( 'urn:flickr:tags' );
```

With a scheme and a label:    

```php
$media->addCategory( )
    ->term( 'Arts/Movies/Titles/A/Ace_Ventura_Series/Ace_Ventura_ -_Pet_Detective' )
    ->scheme( 'http://dmoz.org' )
    ->label( 'Ace Ventura - Pet Detective' );
```    

            

#### Status

The [status](http://www.rssboard.org/media-rss#media-status) of a media item can be one of `active`, `blocked` or `deleted`. When you define the status, you're also expected to provide a reason. The reason can be in the form of a short piece of text, or a URL to a page that describes the reason.

For example, to indicate that the media item is blocked:

```php
$media->status( Status::BLOCKED, 'http://www.reasonforblocking.com' );
```

#### Copyright Information

To add a [copyright notice](http://www.rssboard.org/media-rss#media-copyright):

```php
$media->copyright( 'Copyright 2018 FooBar Media' );
```

You may optionally pass a URL as a second argument to either a terms of use page, or some additional information.

```php
$media->copyright( 
    'Copyright 2018 Foo Bar Media',
    'http://blah.com/additional-info.html'
);
```


#### Dimensions

It's a good idea to supply dimensions, assuming it's a video or audio.

```php
$media->width( 320 )
    ->height( 240 );
```

#### Duration, Bit-rate and Frame rate    
Obviously these may not be relevant, depending the the type of media.

```php
$media
    ->duration( 147 )
    ->bitrate( 128 )
    ->framerate( 24 );
```    

#### The Expression

It's probably not immediately obvious, but the expression states whether a video or audio is:

* The whole thing; e.g. a whole movie or song
* A sample; for example a movie trailer or song excerpt
* Non-stop; e.g. a live stream

To set that:

```php
$media->expression( Media::FULL );
// or
$media->expression( Media::SAMPLE );
// or 
$media->expression( Media::NONSTOP );
```

#### Credits

You can add [credits](http://www.rssboard.org/media-rss#media-credit) to a media item with `addCredit()`. This returns an instance of `Credit` onto which you can attach the entity being credited (e.g. a person or company), the role that entity played and an optional scheme. 

The default scheme is `urn:ebu`, which refers to the European Broadcasting Union Role Codes; for example `actor`, `composer`, `choreographer`, `graphic designer` etc.

Here's an example:

```php
$media->addCredit( )
    ->name( 'John Doe')
    ->role( 'composer' )
    ->scheme( 'urn:ebu' );
```

#### Thumbnails

You can attach [thumbnails](http://www.rssboard.org/media-rss#media-thumbnails) to a video. You can have multiple thumbnails, these can be associated with particular points in the video.

> If time coding is not in play, it's assumed that the thumbnails are in order of importance.

Here's an example:

```php
$media->addThumbnail( )
    ->url( 'http://www.webmonkey.com/images/monkeyrock-thumb.jpg' )
    ->width( 145 )
    ->height( 98 )
    ->time( '12:34' );
```

#### Locations

You can associate one or more geographical locations with a media item. Locations are tied to periods within the media.

For example, suppose you have a movie, and after the opening credits there's a five-minute scene that takes place in London. Here's how you'd model that:

```php
$media->addLocation( )
    ->description( 'London, UK' )
    ->startTime( '03:00' )
    ->endTime( '08:00' )
    ->lat( 51.5074 )
    ->lng( 0.1278 );
```    

> Locations use the GeoRSS extension; the namespace gets registered for you automatically.

#### Community-related Content

You can attach three types of [community-related content](http://www.rssboard.org/media-rss#media-community):

1. Star ratings
2. Statistics; i.e. the number of views and/or "favorites"
3. User-provided tags

Here's an example:

```php
$community = $media->addCommunity( );

$community->addStarRating( )
    ->average( 3.5 )
    ->count( 20 )
    ->min( 1 )
    ->max( 10 );

$community->addStatistics( )
    ->views( 5 )
    ->favorites( 3 );

$community->addTag( 'reuters' )
    ->addTag( 'abc', 3 )
    ->addTag( 'news', 5 )
    ->addTag( 'opinions', 1 );
```

The second argument to `addTag()` is the weight. This might be, for example, the number of users who have assigned that particular tag; the default being 1. 

> The tags must be output in descending order of weight; however this is done automatically, so as you can see from the example, you can add them in any order.

#### Restrictions

You can attach [restrictions](http://www.rssboard.org/media-rss#media-restriction) onto media.

> This element is purely informational and no obligation can be assumed or implied

There are three types of restriction:

1. Whether the media may be shared
2. On a per-country basis
3. By distributor (i.e. a URI)

Restrictions can be `deny` or `allow`; the default is `deny`.

Here are some examples:

```php
$media->addRestriction( )->onSharing( )->deny( );
// is the equivalent of
$media->addRestriction( )->onSharing( );
        
$media->addRestriction( )->allow( )->byCountry( 'au' );

$media->addRestriction( )->byUri( 'http://example.com' );
```

#### Ratings

Not to be confused with star ratings, which come under community content, [ratings](http://www.rssboard.org/media-rss#media-rating) are used to specify the permissable audience. Think `R` vs `PG-13` in the context of movies.

Ratings may have an optional scheme.

Some examples:

```php
$media->addRating( 'nonadult' ); 

$media->addRating( 'adult', 'urn:simple' );

$media->addRating( 'r (cz 1 lz 1 nz 1 oz 1 vz 1)','urn:icra' );
```

> Earlier versions of the MediaRSS specification included a `media:adult` element, but it's been deprecated in favor of the more flexible rating element. As such, only the latter is implemented here.
            
#### Text

You may attach [text](http://www.rssboard.org/media-rss#media-text) to a media item. These can also be associated with periods in the media. For example a transcript, closed captions or the lyrics of a song.

> For transcripts it is encouraged, but not required, that the elements be grouped by language and appear in time sequence order based on the start time.

Here are a couple of examples:

```php
$media->addText( )
    ->content( 'Oh, say, can you see' )
    ->language( 'en' )
    ->start( '00:00:03.000' )
    ->end( '00:00:10.000' );

$media->addText( )
    ->content( 'By the dawn\'s early <strong>light</strong>', 'html' )
    ->language( 'en' )
    ->start( '00:00:10.000' )
    ->end( '00:00:17.000' );
```    

#### Scenes

You may also define [scenes](http://www.rssboard.org/media-rss#media-scenes) within a media item. These have four optional properties; the title, description, and start and end times.

For example:

```php
$media->addScene( )
    ->title( 'sceneTitle1' )
    ->description( 'sceneDesc1' )
    ->startTime( '00:15' )
    ->endTime( '00:45' );
```

#### Comments

To add [comments](http://www.rssboard.org/media-rss#media-comments) that the media as received:

```php
$media->comments( 'comment1', 'comment2', 'comment3' );
```

#### Responses

To list the [responses](http://www.rssboard.org/media-rss#media-responses) that the media has received:

```php
$media->responses( 'response1', 'response1', 'response1' );
```

#### Peer Link

To add a [peer (P2P) link](http://www.rssboard.org/media-rss#media-peerlink):

```php
$media->peerLink( 'http://www.example.org/sampleFile.torrent', 'application/x-bittorrent' );
```

#### Back Links

To add [back links](http://www.rssboard.org/media-rss#media-backlinks); that's to say, all URLs that point to a media object:

```php
$media->addBacklink( 'http://www.backlink1.com' )
    ->addBacklink( 'http://www.backlink2.com' )
    ->addBacklink( 'http://www.backlink3.com' );
```

#### A Note on Multiple Media

You can attach multiple media. If you don't specify which one is the default then it's assumed that it's the first one to appear in the feed. Alternatively, you can all `isDefault()` on an item.

```php
$item->addMedia( )->isDefault( );
```

## WordPress eXtended RSS (WXR)

The library also implements the WordPress eXtended RSS extension, which is used primarily for importing and exporting Wordpress blogs.

You may wonder why you would ever export using the Wordpress format from anything other than Wordpress.

One reason is that the extension provides support for comments; something the basic RSS specification does not.

Moreover, the WordPress eXtended RSS format is used by [Disqus](https://help.disqus.com/developer/custom-xml-import-format) for importing comments from an existing blog. Using this library, therefore, allows you to export all of the comments from your blog implementation into Disqus.

It supports categorization; although these terms and/or tags would generally be lifted from a Wordpress blog, you might find them useful for your  own data export/import needs.

It also provides additional authorship information. Whereas an author in the RSS core spec is typically identified by a string, the WXR extension allows you to provide an ID, login, first & last and/or display name in addition to their e-mail address.

## GeoRSS

The library also supports [GeoRSS Simple](http://www.georss.org/simple.html), which is an extension of RSS &mdash; and, despite what the name suggests &mdash; Atom. It allows you to associate geographical locations with an RSS channel or item, or an Atom feed or entry.

It could be useful if, for example, you write a travel blog and want to be able to identify the place an item is about, in the feed, in a way which might be useful for filtering or mapping.

To use it, call `geoRSS()` on the channel / item / feed / entry, then call a method on the instance of the class it returns. That's probably better explained with an example:

```php
$entry->geoRSS( )->addPoint( 45.256, -71.92 );
```

What we're doing here is associating the point represented by a lat/lng pair.

Alternatively you can associate an entity with a line, polygon or (bounding) box:

```php
$entry->geoRSS( )->addLine( '45.256 -110.45 46.46 -109.48 43.84 -109.86' );
// or
$entry->geoRSS( )->addPolygon( '45.256 -110.45 46.46 -109.48 43.84 -109.86 45.256 -110.45' );
// or
$entry->geoRSS( )->addBox( '42.943 -71.032 43.039 -69.856' );
```

You can also specify the type of feature &mdash; for example, a city &mdash; like this:

```php
$entry->geoRSS( )->featureType( 'city' );
```

You can also name the feature in question:

```php
$entry->geoRSS( )->featureName( 'Manchester' );
```

To define the relationship to the feature or location:

```php
$entry->geoRSS( )->relationshipTag( 'is-centered-on' );
```

There's also this short-cut:

```php
$entry->geoRSS( )->isCenteredOnPoint( 45.256, -71.92 );
```

To define the radius:

```php
$entry->geoRSS( )->radius( 500 );
```

Finally, you can also specify the elevation and / or floor:

```php
$entry->geoRSS( )->elevation( 313 );
```

```php
$entry->geoRSS( )->floor( 2 );
```

> Whenever you call any of these methods, the GeoRSS namespace is automatically added for you.

## Extending the Library

The library ought to be pretty simple to extend. It provides many of the "building blocks" you would need to do so.

If you plan to extend it, here are some pointers that may help.

Internally, the feed is built using PHP's `DomDocument` class.

Everything &mdash; e.g. channels, items, entries, images, enclosures &mdash; is an entity, which is essentially:

- A bunch of properties
- (Fluent) setters
- A method named `element()` which converts the entity into a `DOMElement`

To create your own entities, simply extend the `Entity` class, and implement the abstract `element()` method.

The library makes extensive use of traits; for example there's a trait for titles, one for media, for dimensions and so on. Generally traits provide the properties, the setters and a method to append the appropriate information to a DOM element, that you need to call in your `element()` implementation.

Here's a really simple example:

```php
/**
 * Trait HasTitle
 *
 * @package Lukaswhite\FeedWriter\Traits
 */
trait HasTitle
{
    /**
     * The title
     *
     * @var string
     */
    protected $title;

    /**
     * Set the title
     *
     * @param string $title
     * @return $this
     */
    public function title( string $title ) : self
    {
        $this->title = $title;
        return $this;
    }

    /**
     * Add the title to the specified element.
     *
     * @param \DOMElement $el
     * @return void
     */
    protected function addTitleElement( \DOMElement $el ) : void
    {
        if ( $this->title ) {
            $el->appendChild( $this->createElement( 'title', $this->title ) );
        }
    }
}
```

If you have a look at the `Item` class, for example, you'll see that it utilizes this trait.

Here's an abbreviated version of the `Item` class' `element()` method; note how the `element()` method uses the `addTitleElement()` method:

```php
<?php

namespace Lukaswhite\FeedWriter\Entities\Rss;

/**
 * Class Item
 *
 * @package Lukaswhite\FeedWriter\Entities\Rss
 */
class Item extends Entity
{
    
    /**
     * Create a DOM element that represents this entity.
     *
     * @return \DOMElement
     */
    public function element( ) : \DOMElement
    {
        $item = $this->feed->getDocument( )->createElement( 'item' );

        $this->addTitleElement( $item );
        $this->addDescriptionElement( $item );
        
        // ...rest of method

        return $item;
    }
}
```

Hopefully that's enough for you to get started, should you need to extend it.

## References

- [The RSS 2.0](https://cyber.harvard.edu/rss/rss.html) specification
- [A Guide to Atom](https://validator.w3.org/feed/docs/atom.html)
- The [MediaRSS](http://www.rssboard.org/media-rss) specification
- [Podcast RSS iTunes](https://github.com/simplepie/simplepie-ng/wiki/Spec:-iTunes-Podcast-RSS) specification
- The [GeoRSS Simple](http://www.georss.org/simple.html) specification
- Information about the [Dublin Core](https://feedforall.com/macdocs/html/RSSReferenceDC.html) extension to RSS