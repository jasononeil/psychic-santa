Haxedoc Project
===============

Project Aims
------------

1) Have a user-facing website (haxedoc.org) which is a centralised point for API documentation for both the Haxe Standard Library and various haxelibs.

2) Have the same system be available for use on private or internal projects through the uploading of a haxedoc.xml file.

How it might work
-----------------

### 1. The whole thing is a Ufront app, designed to run on PHP/Neko hosting with a SQLite or MySQL database.

### 2. There are different projects.  Examples might include:

	* Haxe Standard Library (haxedoc.org/p/haxe/)
	* NME					(haxedoc.org/p/nme/)
	* Actuate				(haxedoc.org/p/actuate/)
	* Ufront				(haxedoc.org/p/ufront/)
	* munit					(haxedoc.org/p/munit/)
	* detox					(haxedoc.org/p/detox/)
	* LocalProject			(localhost:2000/p/localproject/)

### 3. Each project might include

	* name (slug, haxelib name)
	* title (a longer title allowing full spaces and capitals etc)
	* homepage (a link to a project homepage, eg haxenme.org, jasononeil.github.com/detox/)
	* sourceURL (a link to an online place where the source can be viewed, maps to class paths)
	* HasMany -> Versions
		* hash -> a hash of the haxedoc.xml + haxelib.xml
		* haxedocXml -> the Xml of all files relevant to this project
		* haxelibXml -> the Xml of the haxelib file (and so, dependencies, owner, tags etc)
		* versionNumber -> pulled from haxelib file
	* HasMany -> Pages (articles, wiki style)
		* Pages are basically wiki-like Markdown article.
	* index -> A single page that serves as the homepage for this project's documentation

   So there are two main things a project can do for their documentation:

    * API Documentation - based on the data in haxedoc.xml
    * Pages - these can be tutorials, introductions, in depth explanations etc.

### 4. API Documentation

The API documentation is automatically generated from haxe's `-xml` output, in the same way that the current Haxe site does this.  However, it will be saved in a way that allows easy versioning, and easy adding of notes, examples, comments etc.

A few features:

 * Automatically import all of the various types data, including comments from the code
 * Allow quick searching of the entire project
 * Links in between the code
 * Filter out type information that comes from the standard library or from a library requirement - link to the canonical page for that info, so that we always have just one document for "String" etc. that has the most up-to-date info.

How the pages are to be structured:

 * List of Packages, List of all Classes (searchable with fuzzy search)
 * One page for each package (classes in this package, subpackages)
 * One page per Type (Class, Interface, Enum, Typedef, Abstract)
 	* Title (name, package, module, link to source code etc)
 	* Description (from code)
 	* Description (user generated)
 	* List of Static Functions      (show/hide inherited)
 	* List of Static Variables      (show/hide inherited)
 	* List of Instance Methods      (show/hide inherited)
 	* List of Instance Variables    (show/hide inherited)
 	* Details on each field
 		* Detailed Type Information
 		* Functions -> description of parameters, return types, errors thrown
 		* User generated note / description
 		* Examples
 	* Whole Class Examples
 	* Comments

User generated content:

 * From the haxedoc.xml, we can pull the class name, field names and types, and in-code documentation
 * On top of this, we will allow the following user generated content through the website:
 	* Class description (detailed, markdown, can have images and links)
 	* Field descriptions (one description per field... if there is in-code documentation both are shown)
 	* Field examples (demonstrate how that specific property or function works, can be more than one example)
 	* Class examples (demonstrate the entire class working together, can be more than one example)
 	* General comments on the class, at the bottom of the file
 * All user generated content is also marked with LowestValidVersion and HighestValidVersion, so that if a comment or example is no longer relevant / accurate, we can leave it for old versions but put in a new one for new versions.

### 5. Pages

Pages are like a wiki - multiple people can edit them, and they are meant for Introductions, Tutorials, Walkthroughs, Explanations etc - they are for long form content.

Model:

	* Page
	* Title (text, also used as slug in URL)
	* Content (Markdown)
	* LowestValidVersion - the first version of the library that this article is relevant to
	* HighestValidVersion - the last version of the library that this article is relevant to
	* History -> Array<{date, user, markdown}> (array of changes over time)

### 6. Users and Signup

Users sign up as users of this app, but we have the opportunity to authenticate against haxelib (using their API), so that an account here could be linked to an account there.  This would allow someone to "take ownership" of a project.

### 7. Getting Content In

To make sure this site isn't a ghost town, we can import the APIs from both the Haxe standard library and from Haxelib.  This could be run as a scheduled task so that the information remains current.

The information from haxelib will be gathered easily enough - the API should be fully reachable for us through neko.  We can then populate all ~377 libraries, and each of their versions :)

### 8. Running your own server

Somebody should be able to set up this app to run on their own server, so that they can use it for documenting internal projects etc.  With this option, they don't have to (but they can if they want to) get all the Haxelibs and the standard library.

They can create their own projects manually, and upload a haxedoc.xml file to import their API.